# Design

## Design philosophy

These skills exploit three well-documented principles:

**Cognitive diversity beats individual depth.** 3-4 distinct perspectives catch 80-90% of defects — diversity matters more than reviewer count (Fagan, IBM 1976). Independent agents with constrained viewpoints outperform a single generalist, the same mechanism behind random forests in ML and the Delphi method in expert forecasting (Surowiecki, "The Wisdom of Crowds").

**Parallel hypothesis testing beats serial investigation.** Enumerating all candidate causes before pursuing any prevents premature convergence (NASA Fault Tree Analysis). Structured post-mortems with parallel tracks reduce repeat incidents by 40-60% (Beyer et al., "Site Reliability Engineering", 2016). A single investigator cannot reliably falsify their own theory due to confirmation bias (Kahneman, "Thinking Fast and Slow").

**Communication overhead must be designed out, not managed.** Communication scales O(n²) with team size (Brooks, "The Mythical Man-Month"). The countermeasure: define interfaces before parallelizing so agents share contracts, not conversations. Interface-first decomposition yields 3-5x throughput gains in teams >4 (Forsgren et al., "Accelerate", 2018).

The **loops** add a fourth: **durable state beats memory.** Goals, progress, counters, and stop signals live
in operational state files rather than only in model context, so work can resume instead of restarting and
stop on recorded evidence rather than a feeling.

<details>
<summary>Full references</summary>

- Agans, D. (2002). *Debugging: The 9 Indispensable Rules for Finding Even the Most Elusive Software and Hardware Problems*. AMACOM.
- Bacchelli, A. & Bird, C. (2013). "Expectations, Outcomes, and Challenges of Modern Code Review." *ICSE 2013*. (Microsoft; n=900+ reviews)
- Beyer, B. et al. (2016). *Site Reliability Engineering*. O'Reilly.
- Brooks, F. (1975). *The Mythical Man-Month*. Addison-Wesley.
- Cataldo, M. et al. (2006). "Identification of Coordination Requirements." *IEEE TSE*.
- Cockburn, A. (2004). *Crystal Clear*. Addison-Wesley. (Walking Skeleton pattern)
- De Bono, E. (1985). *Six Thinking Hats*. Little, Brown and Company.
- Fagan, M. (1976). "Design and Code Inspections." *IBM Systems Journal*.
- Forsgren, N. et al. (2018). *Accelerate*. IT Revolution Press.
- Gawande, A. (2009). *The Checklist Manifesto*. Metropolitan Books.
- Kahneman, D. (2011). *Thinking, Fast and Slow*. Farrar, Straus and Giroux.
- Martin, R.C. (2017). *Clean Architecture*. Prentice Hall.
- McConnell, S. (2004). *Code Complete*. Microsoft Press.
- Ousterhout, J. (2018). *A Philosophy of Software Design*. Yaknyam Press.
- Winters, T. et al. (2020). *Software Engineering at Google*. O'Reilly.
- Zeller, A. (2009). *Why Programs Fail*. Morgan Kaufmann.

</details>

## Design principles

- **Parallel by default** — all agents launch simultaneously, no sequential bottlenecks
- **Minimum floors, not ceilings** — non-trivial parallel commands define a baseline panel and scale up for
  larger tasks; their documented small-change and non-applicable-lens rules may scale down
- **Orthogonal lenses** — no two agents can produce the same finding; each owns one concern
- **Contracts before code** — `implement!` scaffolds interfaces first so parallel agents don't conflict
- **Evidence over opinion** — agents report confidence levels and cite sources/files/lines
- **Synthesize, don't aggregate** — cross-reference findings, flag contradictions, deduplicate
- **State in files, not heads** *(loops)* — goals, progress, and stop signals are durable and resumable
- **Stop cleanly** *(loops)* — halt on goal-met or diminishing returns; always tear down persistence

