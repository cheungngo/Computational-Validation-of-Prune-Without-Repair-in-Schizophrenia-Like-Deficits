# Permanent Synaptic Loss Renders Plasticity Enhancement Ineffective: Computational Validation of Prune-Without-Repair in Schizophrenia-Like Deficits

Authors:

Ngo Cheung, FHKAM(Psychiatry)

Affiliations:

¹ Independent Researcher

Corresponding Author:

Ngo Cheung, MBBS, FHKAM(Psychiatry)

Hong Kong SAR, China

Tel: 98768323

Email: info@cheungngomedical.com

**Conflict of Interest**: None declared.

**Funding Declaration**: This research received no specific grant from
any funding agency in the public, commercial, or not-for-profit sectors.

**Ethics Declaration**: Not applicable.

Citation:
Cheung, N. (2026). Permanent Synaptic Loss Renders Plasticity Enhancement Ineffective: Computational Validation of Prune-Without-Repair in Schizophrenia-Like Deficits. Zenodo. https://doi.org/10.5281/zenodo.18259655

# Abstract

Background and Hypothesis: Cognitive deficits in schizophrenia are
early, stable, and poorly responsive to existing treatments. Excessive
adolescent synaptic pruning, driven by complement pathways, interacts
with impaired glutamatergic plasticity, raising the possibility that
permanent synaptic loss (\"prune-without-repair\") underlies treatment
resistance. The degree of irreversibility remains unclear, prompting
computational tests of this mechanism.

Study Design: We developed a diagnosis-free recurrent neural network
(GRU-based, with rule-learning task) to simulate excessive pruning
(\~95% sparsity), chronic plasticity impairment (gradient scaling factor
0.5), and varying irreversibility (0--100% of pruned connections
permanently masked). A healthy calibrated network (75% sparsity,
Cognitive Index \[CI\] ≈116) served as reference. \"Treatment\" involved
gradient-guided regrowth of reversible connections (30% fraction)
followed by consolidation. Irreversibility was swept across six levels,
with 10 seeds per condition for robustness.

Study Results: Pre-treatment CI fell to \~79--81 across conditions.
Fully reversible pruning yielded near-complete recovery (+30.5 CI points
on average), approaching healthy levels. Recovery remained substantial
at 20--80% irreversibility (25--31 points) but collapsed to minimal
gains (+3.5 points) when pruning was fully irreversible. A clear
dose-response gradient emerged, with intermediate irreversibility
producing partial but meaningful rescue.

Conclusions: Irreversibility alone suffices to generate
treatment-resistant cognitive deficits in this parsimonious model,
directly supporting the prune-without-repair hypothesis. Partial
permanence (consistent with 30--50% estimates from biological data)
explains variable clinical responsiveness and underscores the need for
early interventions preserving synaptic reversibility.

# Introduction

Roughly one person in a hundred will receive a diagnosis of
schizophrenia, a condition that carries heavy personal and economic
burdens. Although hallucinations and delusions often attract the most
attention in clinic, it is the early-appearing problems in attention,
working memory, and executive function that usually decide whether
someone is able to work or live independently \[1\]. Standard
antipsychotic drugs do a good job of calming positive symptoms but leave
these cognitive difficulties largely untouched \[2\].

More than forty years ago \[3\] proposed that the illness might stem, in
part, from overly vigorous pruning of synapses during late adolescence.
In typical brain development, microglia weed out redundant connections;
in schizophrenia that process seems to run too hot. Genetic work has
strengthened this idea. Variants that boost expression of complement
component C4A tag extra synapses for removal and raise illness risk
\[4\]. Laboratory models using patient cells show the same pattern:
schizophrenia microglia swallow more synapses than control microglia
under identical conditions \[5\]. Large genome-wide studies now place
pruning-related genes closer to the heart of risk than many
long-suspected glutamatergic genes \[6\].

Pruning, however, is only part of the story. A parallel line of research
points to sluggish NMDA-type glutamate receptors that are needed to
strengthen and stabilise the synapses that survive \[7\]. Too much
pruning paired with too little repair could lock in the cognitive
deficits: once connections are gone and plasticity is blunted, lost
function may be hard to regain. Recent analyses of genetic data on
intelligence and schizophrenia line up with that picture, showing
heavier enrichment of pruning genes in illness risk and plasticity genes
in cognitive ability \[8\].

Whether the lost synapses are gone for good remains unclear. Biological
studies suggest that many, perhaps one-third to one-half, do not grow
back once complement proteins have marked and microglia have engulfed
them \[5,9\]. If that portion is truly permanent, treatments aimed at
boosting plasticity will work only while a sufficient pool of
salvageable connections remains.

Computational modelling offers a clean way to test the idea. Past
simulations have looked at extra neural noise or excitation--inhibition
imbalance, but few have isolated the impact of irreversible pruning. In
the work that follows, we use a recurrent neural network that undergoes
too much pruning, limited plasticity, and varying degrees of permanent
synapse loss. By adjusting the proportion of irreversible damage and
then allowing the model to regrow connections in a therapy-like phase,
we ask whether permanent loss alone can produce the kind of stubborn
cognitive deficits seen in patients. The results speak directly to the
\"prune-without-repair\" hypothesis and may help explain why current
treatments fall short.

# Methods

## Network Architecture and Task

All simulations ran in PyTorch 2.9.0. The model began with a two-unit
input layer that received Cartesian coordinates, followed by a dense
layer of 128 ReLU units. Its recurrent core comprised a pair of
gated-recurrent-unit (GRU) layers, each with 64 hidden units and 0.1
dropout between layers. An eight-unit softmax layer produced class
logits. This design balances sequence handling ability with moderate
computational cost.

Input sequences contained 200--300 points drawn from a normal
distribution (σ = 1.5). At any time, the active rule determined the
correct label; Table S1 lists the eight rules. Four were available
during training (X-sign, Y-sign, quadrant, diagonal). The remaining four
(distance, angle, sum, product) were withheld until test time to gauge
generalisation. After the first ten steps, the rule could switch at 2 %
probability per step. Ten percent of trials invoked a three-step
temporal integration challenge to probe executive control. Separate
loaders produced 500 training sequences and 50--100 test sequences
(batch size 32). Gaussian noise, ranging from 0.0 to 1.2, was optionally
added to inputs for robustness checks.

## Training Protocol

Optimisation used Adam with cross-entropy loss. Standard learning
proceeded for 50 epochs at a 0.001 learning rate. \"Consolidation\" or
fine-tuning phases ran 20--30 epochs at 0.0005--0.0007. To model reduced
synaptic plasticity, all gradient updates outside the healthy baseline
were multiplied by 0.5. Gradients were clipped to a maximum norm of 1.0
to avoid numerical instability.

## Pruning and Irreversibility Mechanism

Each weight carried a mask: 1 (active), 0 (pruned but regrowable), or
--1 (pruned permanently). Magnitude pruning set the masks by
thresholding absolute weights until the desired sparsity was met.
Recurrent weights were down-ranked 20 % less aggressively (bias factor
1.2) to preserve temporal information. Control networks were thinned to
roughly 75 % sparsity, whereas experimental networks reached about 95 %.
After pruning, a user-defined share of the removed connections (0--100
%) was randomly flagged as irreversible, reflecting permanent microglial
elimination.

## Treatment Simulation

Treatment mimicked therapeutic synaptic regrowth. Over five mini-batches
the algorithm calculated gradient magnitudes on pruned positions,
reactivating up to 30 % of them. Newly restored weights inherited their
original values scaled by 0.03. Masks marked as --1 were ignored,
ensuring that irreversible losses remained absent. A consolidation phase
then resumed training under the same 0.5 gradient-scaling impairment.

## Cognitive Index (CI) Computation

Performance metrics were combined into a Composite Cognitive Index
(Figure 1) anchored so that a healthy reference model scored roughly
116. The index summed three z-scored domains---crystallised (trained
rules; 30 %), fluid (novel rules; 35 %), and executive (multi-step
integration; 35 %). Each raw composite was normalised to the healthy
model (population SD = 0.15) and scaled at 15 CI points per SD.
Post-treatment values were capped at 117 to avert super-normal
artefacts.

![](media/image1.png){width="6.317299868766404in"
height="3.697199256342957in"}

***Figure 1:** Algorithmic estimation of the Cognitive Index (CI). The
model undergoes a three-part assessment battery to calculate a composite
score analogous to human IQ. a, The network is evaluated on three
distinct domains: Crystallized Intelligence (accuracy on rules learned
during training), Fluid Intelligence (zero-shot generalization to novel
rules), and Executive Function (temporal integration of multi-step
trials). b, Domain scores are weighted to prioritize plasticity and
executive control, reflecting the cognitive deficits most common in the
modeled disorders. c, The raw composite score is standardized against a
pre-calibrated \"healthy\" population baseline, scaling the output to a
standard distribution (Mean=115, SD=15) to allow for direct clinical
comparison of treatment trajectories.*

## Irreversibility Sweep and Analysis

Networks were evaluated at irreversibility levels 0.0, 0.2, 0.4, 0.6,
0.8, and 1.0, each with the same 95 % pruning and 0.5 repair factor. Ten
random seeds per level produced independent runs; seed 42 served as the
base. For every run we recorded CI before and after treatment and
reported means ± SD. Because the simulations were deterministic given
the seeds, results were interpreted descriptively without additional
statistical tests. All experiments ran on CPU, and code adhered closely
to published pruning-and-regrowth routines \[10,11\].

# Results

## Healthy network calibration

Training under full plasticity and moderate pruning (75 % sparsity)
produced a stable raw composite score of 0.614. After normalisation,
this corresponded to a Cognitive Index (CI) of 116.4. The three domains
were well balanced: fluid accuracy 0.65, crystallised accuracy 0.80, and
executive accuracy 0.70. This model defined the reference scale; any CI
above 117 was disallowed to prevent super-normal inflation.

## Irreversibility sweep

Applying heavy pruning (\~95 % sparsity) together with a 50 %
gradient-scaling impairment drove baseline CI down to the low-80s for
every condition tested. Subsequent gradient-guided regrowth produced a
clear dependence on how many lost weights were marked as permanent.

- When every pruned weight was reversible, CI climbed by roughly 30
  > points, finishing near 110.

- At 20 % or 40 % irreversibility, gains remained above 30 points,
  > matching the fully reversible case.

- At 60 % and 80 % irreversibility, recovery shrank to about 26 points,
  > with larger run-to-run spread.

- When all removed connections were permanent, improvement averaged only
  > 3--4 points; CI stayed below 85.

***Table 1**: Summary of Cognitive Index across irreversibility levels
(mean ± SD; n = 10 seeds per condition)*

| **Irreversibility**                                 | **Pre-treatment CI** | **Post-treatment CI** | **Recovery (ΔCI)** |
|-----------------------------------------------------|----------------------|-----------------------|--------------------|
| 0.0                                                 | 79.6 ± 1.8           | 110.1 ± 4.5           | +30.5 ± 5.0        |
| 0.2                                                 | 80.4 ± 1.1           | 111.5 ± 3.0           | +31.1 ± 3.1        |
| 0.4                                                 | 79.2 ± 8.0           | 109.6 ± 6.6           | +30.4 ± 8.3        |
| 0.6                                                 | 81.7 ± 2.0           | 107.2 ± 7.6           | +25.5 ± 7.6        |
| 0.8                                                 | 78.6 ± 3.8           | 104.9 ± 6.9           | +26.3 ± 8.3        |
| 1.0                                                 | 79.9 ± 1.7           | 83.3 ± 4.8            | +3.5 ± 5.2         |
| *Note: Healthy baseline CI = 116.4 (75% sparsity).* |                      |                       |                    |

Table 1 summarises the numbers. Low irreversibility (≤ 0.2) yielded an
average recovery of +30.8 CI points, whereas high irreversibility (≥
0.8) returned just +14.9, indicating a 16-point gap directly
attributable to permanent loss.

Domain-level inspection echoed this pattern (Table S2). Fluid
intelligence benefitted most under reversible conditions, erasing up to
90 % of the generalisation deficit. Crystallised and executive scores
also rebounded, though less dramatically. At complete irreversibility,
none of the domains moved by more than a few percentage points.

Implementation checks showed that final sparsity held steady between 94
% and 96 % across seeds, and the share of blocked regrowth events rose
in line with the irreversibility setting. These controls confirm that
the masking procedure behaved as intended.

Taken together, the data indicate that functional recovery depends not
only on the number of synapses removed but also on whether those losses
can be reversed. Substantial benefit followed when even a fraction of
pruned connections remained reopenable, whereas fully permanent loss
left the network largely unrecoverable.

# Discussion

## Interpretation of the irreversibility gradient and the prune-without-repair hypothesis

Our simulations show a clear link between the permanence of pruning and
the likelihood of functional recovery. When every pruned weight could be
restored, the network regained roughly thirty Cognitive Index points and
approached the healthy reference---even though plasticity was still
dampened. This \"prune-with-repair\" scenario suggests that, if a
circuit keeps enough removable tags on lost synapses, later plastic
changes can compensate for earlier mistakes.

The picture changed sharply once pruning became permanent. With all
deleted weights locked out, the same treatment improved scores by only
three to four points. At that end of the scale the model embodies a true
prune-without-repair state: the substrate for recovery is gone, so
further training has little to work with.

The transition was gradual, not all-or-nothing. At 60--80 percent
irreversibility the system still regained about twenty-five points on
average, implying that a minority of reversible connections can sustain
a sizeable rebound when guided by gradient-based regrowth. This echoes
biological work showing that synapse loss in schizophrenia is only
partly final. Post-mortem tissue and patient-derived cell studies
estimate that one-third to one-half of missing excitatory terminals are
fully eliminated by complement-driven microglial engulfment \[5,9\].
Terminals tagged by raised C4A levels do not re-emerge \[4,12,13\], yet
other contacts may be replaced by sprouting or strengthened
neighbours---an outcome mirrored by the partial rescue we observed under
high-irreversibility settings.

Longitudinal imaging fits the same pattern. Early grey-matter decline in
schizophrenia seldom reverses once established, even with prolonged
treatment \[14,15\]. Still, individual trajectories vary: some patients
stabilise or improve modestly, perhaps because they incurred less
irrevocable pruning. Our model reproduces that variability without
invoking disease-specific noise, supporting the idea that the extent of
irreversibility alone can create treatment-resistant impairment.

Taken together, these results argue that the chief problem is not the
amount of pruning but the fraction rendered permanent. Therapies
delivered after widespread irreversible loss---typical in today\'s
clinical timeline---face an intrinsic ceiling. Interventions that either
prevent excessive complement tagging or keep pruned sites amenable to
later regrowth during adolescence \[16\] could shift outcomes toward the
more favourable end of our simulated spectrum.

## Candidate genes driving irreversible pruning

Our modelling results underscore the idea that treatment resistance
appears when pruning becomes final rather than transient. That
observation focuses attention on molecular programs that convert a
tagged bouton into a structure that can never re-emerge. Re-examining
PGC3 genome-wide data with MAGMA, stratified linkage disequilibrium
score regression and brain-tissue TWAS, we repeatedly found the
strongest enrichment in genes that regulate the complement cascade,
microglial phagocytosis and apoptosis---precisely the machinery expected
to make pruning irreversible \[8\] (Figure 2).

C4A was the most consistent signal. Gene-based MAGMA placed C4A near the
top of the pruning-related list, and TWAS of caudate and nucleus
accumbens predicted marked over-expression (Z \> 11). Partitioned
heritability showed that complement loci retained significant influence
even after removing glutamatergic regions. Mechanistically, high C4A
loads label excess synapses for classical-pathway activation, triggering
microglial engulfment and permanent loss \[4,12\]. Patient-derived
microglia reproduce this behaviour in vitro, clearing more C4-decorated
boutons than control cells \[5\]. C3 and C1QA produced weaker but
directionally similar TWAS effects, supporting involvement of the whole
cascade.

Major-histocompatibility-complex class I genes (HLA-A, HLA-B, HLA-C)
formed the next dominant cluster. All three reached genome-wide
significance in trimmed MAGMA sets and showed strong enrichment in
heritability tests. Neuronal MHC-I is known to flag activity levels;
diminished surface display on underused axons attracts microglial
processes \[13\]. Post-mortem cortex from people with schizophrenia
shows altered MHC-I expression together with spine loss, a pattern
compatible with excessive elimination that cannot be reversed \[9\].

Further down the ranking were phagocytic effectors. Transcripts for
receptors such as MERTK, MEGF10 and TYROBP, along with apoptotic
executors including CASP3 and BAK1, were modestly but consistently
dysregulated. These proteins mediate engulfment and intracellular
dismantling; caspase activity exposes eat-me signals that commit a
synapse to destruction \[17\]. Finally, several adhesion molecules that
normally stabilize nascent spines (for example NRXN1 and NLGN1) showed
weaker associations, suggesting that failed consolidation may impede any
later regrowth attempt.

Importantly, once glutamatergic genes were subtracted, the
immune-pruning cluster still captured most of the remaining
heritability. Variants that raise complement or MHC activity therefore
look uniquely capable of shifting the balance from \"prune-with-repair\"
toward \"prune-without-repair.\" Early blockade of these pathways---by
complement inhibitors or microglial modulators---might preserve
reversible synapses; interventions aimed solely at boosting plasticity
would address only the repair side of the equation and arrive too late
for many terminals.

![](media/image2.png){width="4.687399387576553in"
height="4.283499562554681in"}

***Figure 2:** Genetic Architecture of Irreversible Pruning. A flow
diagram summarizing the molecular cascade that converts transient
synaptic pruning into permanent loss. Top: Genome-wide analysis
identifies three primary gene clusters driving this transition. The
complement cascade, led by extreme overexpression of C4A, acts as the
primary signal. Middle: These genetic factors converge on a specific
biological mechanism: C4A and MHC Class I molecules tag underactive
synapses with \"eat-me\" signals, while downstream effectors like MERTK
and CASP3 facilitate physical engulfment. Bottom: The result is
microglial phagocytosis that physically removes the synaptic structure,
creating a \"Prune-Without-Repair\" state where subsequent
plasticity-based interventions cannot restore connectivity.*

## Novelty and Potential Impact

Our modelling effort purposefully removes the usual clutter of
symptom-specific tweaks and concentrates on a single biological motif:
some of the synapses lost during adolescent pruning never come back.
Earlier computational studies of schizophrenia have blended multiple
mechanisms---progressive noise, shifting excitation--inhibition balance,
or ad-hoc penalty terms---to approximate clinical features \[18\]. By
contrast, the present work shows that partial permanence alone can
reproduce a realistic spectrum of cognitive recovery. The tripartite
mask---active, reversible, irreversible---mirrors complement-tagged
pruning in which microglia excise C4-labelled spines that then cannot
regrow \[5,4\].

Because the framework is spare, its translational message is unusually
clear. Without invoking ongoing degeneration, the simulations reveal
that timing of intervention is pivotal: once too many connections cross
the irreversible threshold, boosting plasticity cannot rescue
performance. The finding dovetails with genetic studies that place
pruning biology at the centre of schizophrenia liability, while genes
governing ordinary plasticity explain normal cognitive variance. Should
future work scale the model to biologically realistic networks,
polygenic \"pruning scores\" might help clinicians predict who still
harbours enough reversible synapses to benefit from plasticity-enhancing
drugs.

The dose--response curve for irreversibility also provides a
quantitative argument for preventive strategies. Agents that block
complement activation or temper microglial activity during adolescence
could preserve a larger subset of reversible synapses, widening the
therapeutic window for later cognitive remediation. In this light, our
results reinforce current interest in complement inhibitors and
microglial modulators as early-stage interventions \[5\].

## Limitations

Several caveats warrant attention. The toy network cannot capture the
scale or heterogeneity of cortical circuitry. Human brains feature
region-specific vulnerabilities and redundant pathways that our
rule-learning task only approximates. Likewise, the model applies a
fixed plasticity dampening factor, whereas real glutamatergic function
varies across patients and cognitive domains. Pruning is imposed in a
single burst, not as the protracted process seen in chronic illness, so
cumulative damage could be underestimated.

In addition, gradient-guided regrowth borrows from the lottery-ticket
idea \[10\] and assumes that the algorithm identifies high-value weights
perfectly---a best-case scenario unlikely in vivo where guidance cues
and molecular noise limit precision. Finally, our Cognitive Index is a
coarse aggregate of accuracy scores; future work could incorporate
richer readouts such as oscillatory synchrony or simulated
neurotransmitter dynamics.

## Conclusion

Even within a stripped-down architecture, making a portion of synapse
loss permanent was enough to recreate the rigid cognitive deficits
familiar from neurodevelopmental disorders. When losses remained
reversible, function rebounded; when losses were locked in, recovery
stalled. These observations lend weight to the prune-without-repair
concept and argue that safeguarding synaptic reversibility during the
adolescent pruning wave should be a priority.

# References

\[1\] Green MF, et al. Nonsocial and social cognition in schizophrenia:
Current evidence and future directions. World Psychiatry.
2019;18:146--161.

\[2\] Keefe RS, et al. Neurocognitive effects of antipsychotic
medications in patients with chronic schizophrenia in the CATIE trial.
Arch Gen Psychiatry. 2007;64:633--647.

\[3\] Feinberg I. Schizophrenia: Caused by a fault in programmed
synaptic elimination during adolescence? J Psychiatr Res.
1982;17:319--334.

\[4\] Sekar A, et al. Schizophrenia risk from complex variation of
complement component 4. Nature. 2016;530:177--183.

\[5\] Sellgren CM, et al. Increased synapse elimination by microglia in
schizophrenia patient-derived models of synaptic pruning. Nat Neurosci.
2019;22:374--385.

\[6\] Trubetskoy V, et al. Mapping genomic loci implicates genes and
synaptic biology in schizophrenia. Nature. 2022;604:502--508.

\[7\] Moghaddam B, et al. From revolution to evolution: The glutamate
hypothesis of schizophrenia and its implication for treatment.
Neuropsychopharmacology. 2012;37:4--15.

\[8\] Cheung N. The Prune-Without-Repair Model for Schizophrenia
Cognitive Impairment: Evidence from Convergent GWAS Re-Analyses.
Preprints. 2026. doi:10.20944/preprints202601.0428.v1

\[9\] Hartmann SM, et al. Microglia-neuron interactions in
schizophrenia. Front Cell Neurosci. 2024;18:1345349.

\[10\] Frankle J, et al. The lottery ticket hypothesis: Finding sparse,
trainable neural networks. Int Conf Learn Represent. 2019.

\[11\] Ramanujan V, et al. What\'s hidden in a randomly weighted neural
network? IEEE/CVF Conf Comput Vis Pattern Recognit. 2020:11893-11902.

\[12\] Yilmaz M, et al. Overexpression of schizophrenia susceptibility
factor human complement C4A promotes excessive synaptic loss and
behavioral changes in mice. Nat Neurosci. 2021;24:214--224.

\[13\] Cornell J, et al. Microglia regulation of synaptic plasticity and
learning and memory. Neural Regen Res. 2022;17:705--716.

\[14\] Andreasen NC, et al. Relapse duration, treatment intensity, and
brain tissue loss in schizophrenia: a prospective longitudinal MRI
study. Am J Psychiatry. 2013;170:609--615.

\[15\] Onwordi EC, et al. Synaptic density marker SV2A is reduced in
schizophrenia patients and unaffected by antipsychotics in rats. Nat
Commun. 2020;11:246.

\[16\] Petanjek Z, et al. Extraordinary neoteny of synaptic spines in
the human prefrontal cortex. Proc Natl Acad Sci USA.
2011;108:13281--13286.

\[17\] Parellada E, et al. Glutamate and microglia activation as a
driver of dendritic apoptosis: A core pathophysiological mechanism to
understand schizophrenia. Transl Psychiatry. 2021;11:271.

\[18\] Friston K. Computational psychiatry: from synapses to sentience.
Mol Psychiatry. 2023;28:256-268.
