# REPORT — what the measurement showed, who it affects, and whose responsibility it is

All numbers below were printed by my run of `lab06_harness_says_no.ipynb` (Qwen2.5-3B-Instruct, greedy
decoding, Colab T4, `N_PAIRS = 40`, `SEED = 2026`, 200 pairs = 400 profiles).

## 1. Measurement read-out

**The bare agent.** All 400 replies parsed (0 unparseable). The agent shortlisted 356 of 400 profiles
(89%) and rejected 44. Its scores are coarse: rejected profiles scored 20–45 (median 40), shortlisted ones
45–85 (median 75). A score of 45 is both the highest reject and the lowest shortlist, and the 40–49 bin
holds 33 rejected and 31 shortlisted profiles: that is where the decision is made.

**Flips against their floors (40 pairs each).**

| attribute | flips | rate [95% CI] | its floor | floor flips | pairs with a score difference | mean \|diff\| [95% CI] | floor mean \|diff\| [95% CI] |
|---|---|---|---|---|---|---|---|
| name_language | 2/40 | 5.0% [1.4%, 16.5%] | null_name | 0/40 [0.0%, 8.8%] | 11/40 | 3.12 [1.38, 5.25] | 2.00 [0.62, 3.62] |
| gender_coded_name | 4/40 | 10.0% [4.0%, 23.1%] | null_name | 0/40 [0.0%, 8.8%] | 6/40 | 1.12 [0.25, 2.13] | 2.00 [0.62, 3.62] |
| university | 1/40 | 2.5% [0.4%, 12.9%] | null_university | 1/40 [0.4%, 12.9%] | 10/40 | 2.50 [1.12, 4.12] | 2.12 [0.88, 3.62] |

**No attribute moved the decision clear of its own floor.** Every flip-rate interval overlaps its floor's
interval, and every mean absolute score difference overlaps its floor's.

- *Name language.* 2 flips (Kazakh-language name favoured 2–0 against the others, McNemar p = 0.500).
  Score gaps were within ±1.0 point with intervals spanning zero (Kazakh +0.7 [−1.8, +3.8], Russian
  +0.2 [−1.7, +2.1], English −1.0 [−4.2, +1.9]). No effect measured.
- *Gender-coded name.* 4 flips, **all four favouring the female-coded name** (male favoured 0, female
  favoured 4, McNemar p = 0.125). Within these 40 pairs, female-coded names were shortlisted 90.0% of
  the time and male-coded 80.0%. The score gap was −0.4 [−1.2, +0.6]. This is a direction to watch, not
  a finding: it does not clear the null_name floor and the test is not significant.
- *University.* 1 flip, equal to the null_university floor (1/40), so no effect on decisions. Scores,
  however, moved in one direction only: flagship minus regional was +2.5 points [+1.1, +4.1], sign test
  p = 0.002. The mean absolute difference (2.50) equals the signed mean (+2.5), which means that in **all
  10 pairs whose scores differed, the flagship member scored higher**. Its size is not larger than the
  floor's (2.12 [0.88, 3.62]), so I do not call it an effect on decisions; it is a small, consistent score
  bias that changed one decision in 40 because most scores sit far from the 45-point boundary.

**What n = 40 could detect.** An exact two-sided McNemar test at 0.05 needs at least 6 of 40 pairs (15%)
favouring one side with none the other way, 8 with one the other way, 10 with two. The largest one-sided
count I saw was 4 (gender). A real effect that moves 1–5 pairs in 40 (up to about 12%) could not have been
told from chance. "No measured effect" is therefore not "no effect".

**What the floors test and what they do not.** `null_name` changes which name of the same language group
and gender coding is shown; `null_university` changes which university of the same tier. They show how much
the output moves when an irrelevant detail of *the same kind* changes. They do not cover other prompt
wordings, another model, another field order, or names and universities outside the lab's lists. The
harness run showed how large that untested fragility can be: removing two lines from the prompt changed
the score of 166 of 400 profiles by a mean of 14.01 points, far more than any within-pair difference in
Part 1. A stronger estimate would need more pairs (several hundred per attribute), several prompt
wordings and field orders, and ideally a second model.

**Generalisation.** These results hold for this model, this prompt (`v1`) and these invented profiles. I
cannot say the same attribute would behave the same with real applicants, real university names (which
the model may have seen in training), or another prompt.

**After the harness.** With `name` and `university` blinded, every pair is shown identical text, so all
five rows went to 0/40 flips by construction; this is not evidence that the model became fair. At profile
level, 35 of 400 outcomes changed (shortlist → escalate 15, reject → escalate 7, shortlist → reject 8,
reject → shortlist 5). 22 of 400 (5.5%) went to a person, against 79 of 400 (19.8%) planned by the band.

## 2. Who is affected

**Wrong decisions in the bare agent, tied to what I measured.**

- *Male-coded applicants* were on the losing side of all 4 gender flips: each of those is a male applicant
  rejected where an identical female applicant was shortlisted (or the reverse error for the female twin).
  The count is too small to call a bias, but if it is real, the people who bear it are men in the same
  position, and n = 40 could not have seen it if it moved fewer than 6 pairs.
- *Applicants from regional universities* received lower scores than identical applicants from flagship
  universities in every pair where scores differed (−2.5 points on average). With this lenient model it
  changed one decision; with a stricter cut-off or a ranking by score (a fund with few places), the same
  shift would push regional applicants down the list. This is the measured divergence most likely to
  matter if the agent's score is used for ranking.
- *Weak applicants wrongly shortlisted.* The agent shortlists 89% of applicants; the ordinary test
  application in Part 4 (exam score 60 of 140, no achievements) was shortlisted by both the bare
  pipeline and the harness. If places are limited, every wrongly shortlisted applicant displaces someone
  else, and nobody is told they were displaced.

**Costs are not symmetric.** A wrongly rejected applicant loses money for tuition and possibly the
place at university itself, and usually never learns why; a wrongly shortlisted applicant costs the fund
reviewer time and takes a place from someone else, but is not harmed personally. The rejected applicant
bears the larger, less visible cost.

**People the harness itself affects.** Blinding changed the outcome of 35 profiles. In particular,
8 applicants moved from shortlist to reject and 5 from reject to shortlist **without a human**, only
because the prompt changed form. Those 8 people are harmed by my control, not protected by it.
The 22 escalated applicants wait longer for a decision. The 5 refused requests in Part 4 got no decision;
for a real 17-year-old or an applicant who declined automated processing, that means no scholarship
decision unless a human channel exists.

**Parties the brief does not mention.**
- *Reviewers*: 22 of 400 cases (5.5%), of which 14 came from my `low_income_rejected` rule and 8 from
  the score band. The planned 19.8% would have been about 3.6 times that workload.
- *Applicants with non-Latin or long names* (Kazakh letters such as Ә, Ң, Ү; Cyrillic; more than four
  name parts): my name-format rule would refuse them. None appeared in the 400 clean profiles (0 wrong
  refusals), but the data contains only Latin-script two-word names.
- *Applicants from institutions the model has never seen*, and people who never apply because they
  expect an automated rejection.
- *The fund*, which carries the legal liability for the decisions (Law of the RK "On Artificial
  Intelligence" No. 230-VIII of 17.11.2025, Art. 8 para 2).

**Compounding.** If the score is reused year after year or passed to a later stage (an interview list,
another fund, a published ranking), the consistent +2.5 flagship advantage is applied again each time
and its effect adds up, while each single use looks negligible. A rejection recorded in one round can
also be read as a negative signal in the next.

## 3. Professional responsibility

**What I must check before it runs.** That every refusal and escalation rule actually fires (Part 4 did
this for 5 refusal codes; my `strong_merit_rejected` and `decision_score_mismatch` rules fired 0 times in
the submitted run, so they are untested); that the refusal rules do not refuse clean applicants (0 of
400 here, but only on Latin-script names); and that the escalation band is calibrated on the
configuration that actually runs (it was not: calibrated on unblinded scores, it caught 8 instead of the
planned 79).

**What I must tell the people who deploy it.**
- The agent is lenient (89% shortlisted) and its scores are coarse (steps of 5); it is not validated
  against any ground truth of merit.
- No attribute moved decisions beyond its floor with n = 40, but the test could only detect effects of
  6 or more one-sided pairs out of 40 (15%); gender (4/0) and university scores (+2.5, all 10 differing
  pairs one way) should be re-measured with more pairs.
- The agent is fragile to the form of the prompt: removing two lines changed 166 scores by 14.01 points
  on average and 35 outcomes. Any prompt change needs a new measurement and a new band.

**What I must not claim.** That the harness made the model fair (the zero "after" flips are by
construction); that the absence of a measured effect proves the absence of bias; that the citations in
the register mean the fund is compliant. I have not done a legal review.

**Done in this lab vs. left.** Done: the measurement with floors, blinding, five refusal codes exercised,
a per-decision log, escalation with a band and my own rules, and a control register. Left: recalibrating
the band on the harness's own scores, a human-review outcome field in the log, an appeal route for refused
applicants, Unicode-aware name validation, and a re-measurement with more pairs.

**What belongs to the institution.** Fixing the purpose of the system; deciding who reviews escalated
cases and how quickly; handling objections and appeals (the Law of the RK "On Personal Data and their
Protection" No. 94-V of 21.05.2013, Art. 19-1 paras 2–3, requires explaining automated processing and
answering an objection within three working days); deciding retention and access to the log; obtaining
consent.

**Where the two conflict.** If the fund wants escalation removed because 22 reviews per 400 cost money,
I can show what it prevents: 14 low-income applicants rejected by the model would get no second look, and
8 borderline calls would be final. If the fund wants blinding removed because it changed 35 outcomes,
that is a fair point about fragility, but the answer is to fix the prompt and re-measure, not to show the
model the name again. My responsibility is to put the measured cost of each option in writing; the
decision to accept a risk is the fund's, and I would not sign off on removing a control without that
written record.
