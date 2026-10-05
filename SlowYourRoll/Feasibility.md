# Slow Your Roll — Feasibility Study

**Idea:** record phone acceleration (IMU) plus simultaneous video of an Aikido forward roll (*mae ukemi*), then use numerical analysis and a little AI to help people improve their rolling.

**Scope assumed:** forward roll, left and right sides, consumer phones (own IMU) and consumer cameras (30/60/120 fps). The repo's existing Roll Smoothness Analyzer (`js/analysis.js`) is the starting point.

**Method note:** web search only. Several full texts (PMC, Springer, ScienceDirect, arXiv) were blocked, so participant counts of cited studies are **not verified**. Feasibility percentages and data-volume figures are my own estimates, with the reasoning shown.

---

## 1. Bottom line

| Goal | Feasibility | Participants | Rolls | Net rolling time |
|---|---|---|---|---|
| **A. Objective metrics** (phone IMU + video review; validate against expert ratings) | **High (~85%)** | 30–40 | ~1,200 | ~1 h |
| **B. Automated fault detection** ("you hit your spine", "collapsed arm", "uneven speed") | **Moderate (~40–50%)** | 60–100 | 2,000–4,000 | 2–4 h |
| **C. Generalises across dojos, phones, styles** | **Moderate-low (~30%)** | 150–200+ | ~10,000 | ~8 h |

Net rolling time is small. The real costs are **expert labeling (60–200 h)** and **recruiting participants across dojos**.

---

## 2. Has it been done?

No published or commercial system found that fuses phone IMU and synced video to coach Aikido or judo rolls. Adjacent work exists:

| Area | What exists | Gap for rolling |
|---|---|---|
| Aikido forward roll | Force-plate study: experts reduce ground impact vs untrained; untrained exceeded **3.5× body weight**. Vicon tracking was dropped because of occlusion/unreliability ([ResearchGate](https://www.researchgate.net/publication/284898172_The_aikido_inspiration_to_safety_and_efficiency_An_investigation_on_forward_roll_impact_forces)) | Lab-only; no phone, no coaching loop |
| Judo ukemi | 3D biomechanics; novice vs experienced differ ([systematic review](https://pmc.ncbi.nlm.nih.gov/articles/PMC8998643/)); mae-ukemi kinematics ([KJAB](https://www.koreascience.or.kr/article/JAKO200220163389341.view?orgId=anpor&hide=breadcrumb%2Cjournalinfo)) | Lab motion capture, not consumer gear |
| Fall-technique training | Martial-arts fall training reduces hip impact in naive subjects after brief training ([ResearchGate](https://www.researchgate.net/publication/6075404_Martial_arts_fall_techniques_reduce_hip_impact_forces_in_naive_subjects_after_a_brief_period_of_training)) | Confirms improvement is measurable |
| Tumbling (closest analogue) | A single IMU plus ML identifies and assesses tumbling elements in cheer ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC11991202)) | Element recognition, not technique coaching |
| Striking sports | IMU classification of boxing punches and kicks ([boxing](https://www.sciencedirect.com/science/article/pii/S1568494625010580), [kicks](https://thesai.org/Downloads/Volume16No6/Paper_14-Dataset_Development_for_Classifying_Kick_Types.pdf)) | Different motion; wrist/glove sensors |
| Grappling video | Pose-based BJJ position classification; one dataset has 120,279 frames, 18 classes ([GitHub](https://github.com/talhaahussain/grappling-pose-identification), [Submitsu](https://www.submitsu.com/resources/data/datasets)) | Position labels, not roll quality; heavy occlusion |
| Smartphone mocap | OpenCap: two iPhones, ~4.5° mean joint-angle error on walking/squat/drop-jump ([PLOS Comp Bio](https://journals.plos.org/ploscompbiol/article?id=10.1371%2Fjournal.pcbi.1011462)) | Not validated on inverted/rolling motion |
| Martial-arts apps | Senseii.ai (boxing, Muay Thai, karate, BJJ), Shotokan stance app ([Sci Rep](https://www.nature.com/articles/s41598-026-41414-5)) | Static stances and strikes; no ukemi |
| Smoothness metrics | LDLJ-A (log dimensionless jerk on acceleration) works for discrete movements; velocity-based metrics drift when integrated from IMU ([PMC](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7841375/)) | Directly applicable to a roll |

**Existing app review:** `calculateRollMetrics()` (`js/analysis.js:154`) takes jerk from raw sample-to-sample differences of acceleration magnitude. That is noisy and depends on sampling rate. Its "smoothness" score uses a hard-coded 50 m/s³ threshold with no expert validation. This is the first thing to replace.

---

## 3. Proposed architecture (three tiers)

1. **Numerical (no AI):** filter, fuse accel and gyro into trunk orientation, segment the roll (launch, shoulder contact, back, hip/foot contact, rise). Metrics: duration, impact peak(s) and count, rotation-rate profile, LDLJ-A, left/right asymmetry, time to standing.
2. **Video features:** pose estimation (e.g., MediaPipe, ViTPose) for head-to-floor distance, arm shape, spine line, contact path. The video is also the labeling tool for experts.
3. **AI:** gradient-boosted trees or a small 1D-CNN on tier-1 and tier-2 features, trained on expert labels. An LLM turns flagged faults into coaching language and needs no extra training data.

---

## 4. Data requirements

### Assumptions
- Net roll window ≈ 3 s; trimmed clip ≈ 8 s; raw continuous capture ≈ 30 s per roll including walk-back and re-sync.
- ~25 min per participant session (consent, strap-on, 15–25 rolls). Fatigue and safety cap rolls per session.

### Sizing logic
- **Experts vs novices:** 17 per group detects Cohen's d = 1.0 (80% power, α = 0.05); 26 per group for d = 0.8 (normal approximation, computed).
- **Reliability (ICC):** ~30 participants with repeat sessions is the usual minimum.
- **Fault classifier:** ≥10 events per feature; with ~20 engineered features that is ~200 positives per fault class. At ~15% fault prevalence this means 1,300+ rolls per fault, and multiple faults share rolls, so 2,000–4,000 rolls.
- **Person-independent evaluation:** cross-validate by participant, never by roll, or accuracy will be inflated. This needs ≥50 participants so each fold has ≥10 people.

### Totals

| Stage | Participants | Rolls | Net rolling | Trimmed clips | Raw capture | Station-hours | Expert labeling* |
|---|---|---|---|---|---|---|---|
| Pilot (sync, placement, pose-failure test) | 10 | 200 | 0.2 h | 0.4 h | 1.7 h | ~4 h | ~3 h |
| **A. Metric validation** | 30–40 | 1,200 | 1 h | 2.7 h | 10 h | ~35 h | ~20 h |
| **B. Fault classifier** | 60–100 | 3,200 | 2.7 h | 7 h | 27 h | ~70 h | ~60 h |
| **C. Robust / multi-dojo** | 150–200+ | 10,000 | 8.3 h | 22 h | 83 h | ~170–250 h | ~170–200 h |

\*Two raters at ~30 s per roll, plus adjudication of ~20% disagreements.

---

## 5. Key risks

1. **No ground truth for "good roll."** Aikikai, Yoshinkan and other schools roll differently (hand placement, height, slap vs no slap). If expert-rater agreement is low (Cohen's κ < ~0.6), the ML ceiling is low. Measure rater agreement in the pilot.
2. **Pose estimation on rolls is unproven.** My searches found no direct evidence for inverted or on-the-floor poses. Expect failures from inversion, occlusion, loose gi/hakama and motion blur. Test in the pilot: % frames with valid detection on ~20 rolls.
3. **Sensor limits.** The iOS public API reportedly exposes ~100 Hz, and Android rates vary ([discussion](https://developer.apple.com/forums/thread/67639)). Accelerometer range may clip on impacts (many phones are ±8 g; verify per model).
4. **Placement and safety.** A rigid phone on the back is hit by the roll. Prefer chest-front or belt-front, which also raises the question of how placement affects readings.
5. **Sync.** Phone and camera clocks drift. Use a clap/tap at the start and end of each clip (visible and audible, with an accel spike); 60–120 fps video keeps error under ~1 frame.
6. **Human-subjects issues.** Falls and injuries, consent and waivers, video privacy (faces), and ethics review if you plan to publish.
7. **Recruiting.** 60–100 participants likely means 6–10 dojos.

---

## 6. Suggested next steps (no code yet)

1. **Pilot, ~10 people, 1 day:** test sync method, 2–3 phone placements, 60 vs 120 fps, and pose-estimator failure rate.
2. **Define the rubric:** have 2–3 senseis agree on a 5–8 item roll checklist; score 50 rolls and compute κ.
3. **Decide the claim:** "smoothness and impact feedback" (Goal A) is defensible now; "fault diagnosis" (Goal B) needs the labeled dataset.
4. **Fix the analyzer baseline:** replace raw-difference jerk with filtered LDLJ-A and add CSV/video-timestamp export.

---

## Sources
Inline links above. Searches covered IMU martial-arts classification, BJJ pose estimation, judo/aikido ukemi biomechanics, tumbling IMU, OpenCap, smartphone sampling-rate limits, smoothness metrics, and martial-arts coaching apps.
