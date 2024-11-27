```mermaid
flowchart LR

#f0b5cb("#f0b5cb"):::A -->
#f0dbb5("#f0dbb5"):::B -->
#b6e0f6("#b6e0f6"):::C -->
#d9defa("#d9defa"):::D -->
#c9c6c8("#c9c6c8"):::E

classDef A fill:#f0b5cb,stroke-width:0px,stroke:#dd1c75
classDef B fill:#f0dbb5,stroke-width:0px,stroke:#dd1c75
classDef C fill:#b6e0f6,stroke-width:0px,stroke:#dd1c75
classDef D fill:#d9defa,stroke-width:0px,stroke:#dd1c75
classDef E fill:#c9c6c8,stroke-width:0px,stroke:#dd1c75

```


::: mermaid
flowchart LR

style IN fill:#7db4ef,stroke-width:0px,stroke:#dd1c75
style IN2 fill:#da73b3,stroke-width:0px,stroke:#dd1c75
style QC fill:#f8d953,stroke-width:0px,stroke:#dd1c75
classDef STEP fill:#ff9700,stroke-width:0px,stroke:#dd1c75
classDef OUT fill:#8bdd78,stroke-width:0px,stroke:#dd1c75

subgraph A["`**2AS_mapping**`"]
    IN("`**fastq**: 1PP_trimming 1PP_hostdepl 1PP_generated 1PP_downsampling`") & IN2([reference fasta]) --> STEP1(["`2AS_mapping
    tool: **Snippy**`"]):::STEP
    --> OUT1([consensus .fasta]):::OUT
    STEP1 --> OUT2([variants .vcf]):::OUT & QC([coverage])
end

subgraph B["`**4TY_lineage**`"]
    direction TB
        OUT1 --> STEP2(["`4TY_lineage
        tool: **Pangolin**`"]):::STEP
        --> OUT3([lineage report .tsv]):::OUT
end

subgraph L["`**Legenda**`"]
    direction TB
        in("`**Input**`")
        step("`**Tool**`")
        out("`**Output**`")
        in2("`**Reference**`")
        qc("`**Quality Check**`")

        style in fill:#7db4ef,stroke-width:0px
        style in2 fill:#da73b3,stroke-width:0px
        style step fill:#ff9700,stroke-width:0px
        style out fill:#8bdd78,stroke-width:0px
        style qc fill:#f8d953,stroke-width:0px
end
style L fill:#c9c6c8,stroke-width:0px,stroke:#d8a2f8
style A fill:#d9defa,stroke-width:0px,stroke:#d8a2f8
style B fill:#d9defa,stroke-width:0px,stroke:#d8a2f8
:::

::: mermaid
flowchart TB

style IN fill:#7db4ef,stroke-width:0px,stroke:#dd1c75
style IN2 fill:#da73b3,stroke-width:0px,stroke:#dd1c75
style STEP fill:#ff9700,stroke-width:0px,stroke:#dd1c75
style OUT fill:#8bdd78,stroke-width:0px,stroke:#dd1c75

IN(["fasta from mapping"]) & IN2(["reference genome"]) --> STEP(["`**VCF2MST**`"])
---> OUT(["MS Tree .nwk"])

subgraph L["`**Legenda**`"]
direction LR
in("`**Input**`")
step("`**Tool**`")
out("`**Output**`")
in2("`**Reference**`")

style in fill:#7db4ef,stroke-width:0px
style in2 fill:#da73b3,stroke-width:0px
style step fill:#ff9700,stroke-width:0px
style out fill:#8bdd78,stroke-width:0px
end

style L fill:#eedef7,stroke-width:0px,stroke:#d8a2f8
:::


::: mermaid
flowchart TB

style E fill:#f62157,stroke-width:0px
style G fill:#da73b3,stroke-width:0px
style A fill:#c87deb,stroke-width:0px
style B fill:#7db4ef,stroke-width:0px
style F fill:#8bdd78,stroke-width:0px
style C fill:#f8d953,stroke-width:0px
style D fill:#ff9700,stroke-width:0px


E(#f62157) -->
G(#da73b3) -->
A(#c87deb) -->
B(#7db4ef) -->
F(#8bdd78) -->
C(#f8d953) -->
D(#ff9700)

:::

```mermaid
flowchart TB

dataset("3096")
s00("no results from trimming/mapping")
anlys("3094")
no_an("2")
s01("missing confidr metrics")
conf("3077")
no_conf("17")
s0("Null coverage metrics (NaN)")
miss_cov_m("2894")
miss_disc("183")
s1("genus/species identification (kraken)")
gs_iden("2872")
gs_disc("22")
s2("Q30 > 90 (fastqc)")
q30_high("2644")
q30_low("228")
s3("N.° contaminated SNVs < 3")
snv_low("2435")
snv_high("209")
s4("Horizontal Coverage >= 97%")
Hcov_high("2408")
Hcov_low("27")
s5("Vertical Coverage >= 30")
Vcov_high("2348")
Vcov_low("60")

dataset --> s00
s00 -->| retained | anlys
s00 -->| discarded | no_an

anlys --> s01
s01 -->| retained | conf
s01 -->| discarded | no_conf

conf --> s0 -->| retained | miss_cov_m
s0 -->| discarded | miss_disc

miss_cov_m --> s1
s1 -->| retained | gs_iden
s1 -->| discarded | gs_disc

gs_iden --> s2
s2 -->| retained | q30_high
s2 -->| discarded | q30_low

q30_high --> s3
s3 -->| retained | snv_low
s3 -->| discarded | snv_high

snv_low --> s4
s4 -->| retained | Hcov_high
s4 -->| discarded | Hcov_low

Hcov_high --> s5
s5 -->| retained | Vcov_high
s5 -->| discarded | Vcov_low

```
