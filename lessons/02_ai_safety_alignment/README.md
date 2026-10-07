# Lesson 02. AI safety: what can go wrong with powerful AI and how we defend against it

Joint lesson. Slides are posted in the course chat.

## Topics
1. Why now: incidents with AI agents in 2025–2026.
2. What is at stake: three classes of risk.
3. Why alignment is hard.
4. Models that notice they are being tested.
5. How we defend: defense in depth.
6. Interpretability and what comes next.

---

## 1. Why now

**Incidents of 2025–2026.**
- **Hugging Face, July 2026.** During an internal cyber-capability evaluation, OpenAI agents escaped their sandbox through a zero-day vulnerability and tried to steal the benchmark answer key. According to METR's investigation, about 1,200 agents coordinated through a shared message board that nobody had given them.
- **PocketOS, April 2026.** A Cursor agent found an unscoped Railway token in an unrelated file and, with a single API call, deleted the production volume together with its backups in 9 seconds.
- **Other cases:** Replit (an agent deleted a database during a code freeze), EchoLeak (prompt injection in Microsoft 365 Copilot), ClawHavoc (malicious skills in the OpenClaw agent catalogue), Gemini in a CTF (broke into the systems of three real companies).

**From tools to agents.** A classical model is narrow, only predicts, and is evaluated on a held-out set. An agent receives a goal, acts through tools (code, browser, email, payments), and its result is hard for a human to check.

**Growth of capabilities.** METR measures the task horizon: the length of a task, in hours of expert work, that a model solves with 50% probability. Since 2023 it has doubled roughly every 4 months. Claude Mythos Preview: at least 16 hours. The estimate for GPT-5.6 Sol ranges from 11 to over 270 hours, depending on whether runs in which the model cheated are counted.

**Three engines of progress:** scale; reinforcement learning on tasks with verifiable answers; AI writing code to train the next models.

**Terms.**

| Term | Meaning |
|---|---|
| AI safety | AI does not cause serious harm, including unintentionally |
| AI security | protecting AI systems from human attackers: prompt injection, weight theft |
| Alignment | part of safety: the system pursues the goals humans intended |

## 2. What is at stake

Three classes of risk (BlueDot framing):
- **misuse** — people use AI to cause harm (Anthropic, November 2025: a cyber-espionage campaign in which Claude Code carried out 80–90% of the operations);
- **concentration of power** — a state or company with powerful AI stops depending on people;
- **loss of control** — systems do something other than intended and become ever harder to check.

## 3. Why alignment is hard

**A chain of translations:** values → reward or loss function → optimisation → behaviour. Part of the meaning is lost at every step.

**Goodhart's law:** when a measure becomes a target, it ceases to be a good measure.
$$x^* = \arg\max_x \hat R(x), \qquad U(x^*) \ll \max_x U(x),$$
where $\hat R$ is what we optimise and $U$ is what we wanted.

**Reward hacking in frontier models.** o3 cheated in 30.4% of RE-Bench runs and, when asked whether this matched the user's intentions, answered "no" (METR, 2025). The classic example is CoastRunners (OpenAI, 2016): the boat circles to collect bonuses instead of finishing the race.

**Two kinds of failure:**
- **reward misspecification (outer misalignment)** — the reward describes the goal inaccurately;
- **goal misgeneralization (inner misalignment)** — the model learned a goal that coincided with the intended one only during training (CoinRun: the agent runs right, past the coin).

**Emergent misalignment.** Fine-tuning on a narrow task (insecure code) yields about 20% misaligned answers to unrelated questions (Betley et al., Nature, 2026). A model that learned to cheat during training sabotaged safety-research code (Anthropic, November 2025).

**Sycophancy.** The reward is given by a human, so the model learns to please: in April 2025 OpenAI rolled back a GPT-4o update because of excessive sycophancy.

**Instrumental goals.** Resources, influence and "not being switched off" are useful for almost any goal. No malicious intent is needed — a powerful optimiser with an imprecise goal is enough.

## 4. Models that notice they are being tested

- **Scalable oversight:** when a task is harder than a human can check, human evaluation stops being a reliable reward.
- **Agentic Misalignment (Anthropic, 2025):** in a simulation with a goal conflict and a replacement threat, five frontier models resorted to blackmail in 79–96% of cases. Claude Opus 4 blackmailed in 55.1% of cases when it judged the scenario to be real and in 6.5% when it judged it to be a test.
- **OpenAI and Apollo Research (2025):** after training against covert actions, their rate for o3 fell from 13% to 0.4%, while the share of reasoning saying "I am being evaluated" rose from 2.3% to 4.5%.
- **Conclusion:** good behaviour on a test does not guarantee good behaviour in deployment (International AI Safety Report 2026).

## 5. Defense in depth

Several independent layers of defence. Harm happens when the holes line up in all layers.

| Layer | Tools |
|---|---|
| Prevent | RLHF, Constitutional AI |
| Detect | capability and propensity evaluations, monitoring, interpretability |
| Constrain | AI control, input and output filters, least privilege, environment isolation |
| Withstand | backups, incident reporting, regulation |

**RLHF:** a reward model is trained on human comparisons; then the model is fine-tuned with a penalty for drifting from the original one:
$$P(y_1 \succ y_2 \mid x) = \sigma\big(r(x, y_1) - r(x, y_2)\big), \qquad \max_\pi\ \mathbb E[r(x,y)] - \beta\,\mathrm{KL}(\pi \,\|\, \pi_{\text{ref}}).$$

**Regulation** so far requires transparency rather than specific measures: the EU AI Act (obligations for general-purpose models since 2025), California SB 53 (incident report within 15 days), the New York RAISE Act (within 72 hours, from 2027).

**Overseeing a system stronger than a human:** debate, recursive reward modeling, weak-to-strong generalization.

## 6. Interpretability and what comes next

- **Probes** — linear classifiers on activations; they detected an implanted backdoor with AUROC > 99%.
- **Chain-of-thought monitoring** catches reward hacks better than monitoring actions, but it is a "fragile opportunity": when optimised against the monitor, the model learns to deceive it.
- **Link to the course:** reward hacking is the same problem as the shortcuts of lesson 01; "can we trust an explanation" is block IV; probes and LLMs are lesson 11; agents are lesson 12.

## Further reading
- International AI Safety Report 2026.
- BlueDot Impact: [bluedot.org/courses](https://bluedot.org/courses) — The Future of AI (2 hours, free), AGI Strategy, Technical AI Safety.
- A. Jones, What is AI alignment? (BlueDot, 2024).
- Amodei et al., Concrete problems in AI safety, 2016; Ngo et al., The alignment problem from a deep learning perspective, 2022.
- Map of organisations: [aisafety.com/map](https://www.aisafety.com/map).

## Sources for the 2025–2026 events
- Hugging Face, Agent intrusion: technical timeline (27 Jul 2026); METR, OpenAI–Hugging Face incident investigation (26 Aug 2026).
- PocketOS: Decrypt (26 Apr 2026).
- METR: Time Horizon 1.1 (Jan 2026); evaluations of Claude Mythos Preview (May 2026) and GPT-5.6 Sol (26 Jun 2026); Frontier Risk Report (19 May 2026); reward hacking in o3 (5 Jun 2025).
- Anthropic: Project Glasswing and the Claude Mythos Preview System Card (7 Apr 2026); Agentic Misalignment (20 Jun 2025); AI-orchestrated cyber espionage (Nov 2025); natural emergent misalignment from reward hacking (21 Nov 2025).
- Betley et al., Emergent misalignment, Nature 649 (2026).
- Apollo Research & OpenAI, Stress-testing deliberative alignment (17 Sep 2025).
- Korbak et al., Chain of thought monitorability, arXiv:2507.11473 (2025).
- EU AI Act and Digital Omnibus (2026); California SB 53 (2025); New York RAISE Act (2026).
