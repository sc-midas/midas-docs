# Preparation

## Developing and test environment

System: Linux Ubuntu 18.04

Programming language:

- Python 3.7.11
- R 4.1.1

Main packages:

- PyTorch 1.12.0
- Seurat 4.1.0
- Signac 1.6.0

## Quality control for individual datasets (27 batches in total)

```bash
# dogma (4 batches)
Rscript preprocess/atac+rna+adt_dogma_lll_ctrl.R &
Rscript preprocess/atac+rna+adt_dogma_lll_stim.R &
Rscript preprocess/atac+rna+adt_dogma_dig_ctrl.R &
Rscript preprocess/atac+rna+adt_dogma_dig_stim.R &

# tea (5 batches)
Rscript preprocess/atac+rna+adt_tea_w1.R &
Rscript preprocess/atac+rna+adt_tea_w3.R &
Rscript preprocess/atac+rna+adt_tea_w4.R &
Rscript preprocess/atac+rna+adt_tea_w5.R &
Rscript preprocess/atac+rna+adt_tea_w6.R &

### tea multiome (2 batches)
Rscript preprocess/atac+rna_tea_multiome_w1.R &
Rscript preprocess/atac+rna_tea_multiome_w2.R &

### 10x multiome (4 batches)
Rscript preprocess/atac+rna_10x_multiome_arc2_3k.R &
Rscript preprocess/atac+rna_10x_multiome_arc2_10k.R &
Rscript preprocess/atac+rna_10x_multiome_chrom_c_10k.R &
Rscript preprocess/atac+rna_10x_multiome_chrom_x_10k.R &

# asap (2 batches)
Rscript preprocess/atac+adt_asap_ctrl.R &
Rscript preprocess/atac+adt_asap_stim.R &

### asap cite (2 batches)
Rscript preprocess/rna+adt_asap_cite_ctrl.R &
Rscript preprocess/rna+adt_asap_cite_stim.R &

### wnn cite (8 batches)
Rscript preprocess/rna+adt_wnn.R &
```
