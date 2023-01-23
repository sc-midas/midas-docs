# Knowledge transfer

## Reference construction

### Generating training data for MIDAS

Generating reference dataset for evaluating knowledge transfer, where the dogma datasets are excluded:

```bash
Rscript preprocess/combine_subsets.R --task atlas_no_dogma  && py preprocess/split_mat.py --task atlas_no_dogma  &
```

### Training MIDAS

```bash
CUDA_VISIBLE_DEVICES=5 py run.py --exp e0 --task atlas_no_dogma
```

### Inferring latent variables, and generating imputed and batch corrected counts

```bash
CUDA_VISIBLE_DEVICES=1 py run.py --task atlas_no_dogma  --act predict_all_latent_bc --init_model sp_latest &
```

## Mosaic integration with fewer modalities

The goal of this experiment is to generate the baseline for transfer learning comparison.

### Generating training data for MIDAS

```bash
Rscript preprocess/combine_subsets.R --task dogma_single_atac  && py preprocess/split_mat.py --task dogma_single_atac &
Rscript preprocess/combine_subsets.R --task dogma_single_rna   && py preprocess/split_mat.py --task dogma_single_rna &
Rscript preprocess/combine_subsets.R --task dogma_single_adt   && py preprocess/split_mat.py --task dogma_single_adt &
Rscript preprocess/combine_subsets.R --task dogma_paired_a     && py preprocess/split_mat.py --task dogma_paired_a  &
Rscript preprocess/combine_subsets.R --task dogma_paired_b     && py preprocess/split_mat.py --task dogma_paired_b  &
Rscript preprocess/combine_subsets.R --task dogma_paired_c     && py preprocess/split_mat.py --task dogma_paired_c  &
```

### Training MIDAS

De novo training on the additional dogma mosaic datasets (for comparison):

```bash
CUDA_VISIBLE_DEVICES=0 py run.py --exp e0 --task dogma_single_atac &
CUDA_VISIBLE_DEVICES=1 py run.py --exp e0 --task dogma_single_rna &
CUDA_VISIBLE_DEVICES=2 py run.py --exp e0 --task dogma_single_adt &
CUDA_VISIBLE_DEVICES=3 py run.py --exp e0 --task dogma_paired_a &
CUDA_VISIBLE_DEVICES=4 py run.py --exp e0 --task dogma_paired_b &
CUDA_VISIBLE_DEVICES=5 py run.py --exp e0 --task dogma_paired_c &
```

### Inferring latent variables, and generating imputed and batch corrected counts

```bash
CUDA_VISIBLE_DEVICES=0 py run.py --task dogma_single_atac --act predict_all_latent_bc --init_model sp_00001899 --exp e0 &
CUDA_VISIBLE_DEVICES=1 py run.py --task dogma_single_rna  --act predict_all_latent_bc --init_model sp_00001899 --exp e0 &
CUDA_VISIBLE_DEVICES=2 py run.py --task dogma_single_adt  --act predict_all_latent_bc --init_model sp_00001899 --exp e0 &
CUDA_VISIBLE_DEVICES=3 py run.py --task dogma_paired_a    --act predict_all_latent_bc --init_model sp_00001899 --exp e0 &
CUDA_VISIBLE_DEVICES=4 py run.py --task dogma_paired_b    --act predict_all_latent_bc --init_model sp_00001899 --exp e0 &
CUDA_VISIBLE_DEVICES=5 py run.py --task dogma_paired_c    --act predict_all_latent_bc --init_model sp_00001899 --exp e0 &
```

## Model transfer

### Generating training data for MIDAS

Generating 14 different dogma mosaic datasets for querying:

```bash
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_full_transfer         && py preprocess/split_mat.py --task dogma_full_transfer        &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_paired_full_transfer  && py preprocess/split_mat.py --task dogma_paired_full_transfer &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_single_full_transfer  && py preprocess/split_mat.py --task dogma_single_full_transfer &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_paired_abc_transfer   && py preprocess/split_mat.py --task dogma_paired_abc_transfer  &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_paired_ab_transfer    && py preprocess/split_mat.py --task dogma_paired_ab_transfer   &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_paired_ac_transfer    && py preprocess/split_mat.py --task dogma_paired_ac_transfer   &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_paired_bc_transfer    && py preprocess/split_mat.py --task dogma_paired_bc_transfer   &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_single_transfer       && py preprocess/split_mat.py --task dogma_single_transfer      &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_single_atac_transfer  && py preprocess/split_mat.py --task dogma_single_atac_transfer &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_single_rna_transfer   && py preprocess/split_mat.py --task dogma_single_rna_transfer  &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_single_adt_transfer   && py preprocess/split_mat.py --task dogma_single_adt_transfer  &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_paired_a_transfer     && py preprocess/split_mat.py --task dogma_paired_a_transfer    &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_paired_b_transfer     && py preprocess/split_mat.py --task dogma_paired_b_transfer    &
Rscript preprocess/combine_unseen.R --reference atlas_no_dogma --task dogma_paired_c_transfer     && py preprocess/split_mat.py --task dogma_paired_c_transfer    &
```

### Training MIDAS

Transfer learning on query dogma mosaic datasets:

```bash
CUDA_VISIBLE_DEVICES=0 py run.py --task dogma_full_transfer         --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=1 py run.py --task dogma_paired_full_transfer  --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=2 py run.py --task dogma_paired_abc_transfer   --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=3 py run.py --task dogma_paired_ab_transfer    --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=4 py run.py --task dogma_paired_ac_transfer    --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=5 py run.py --task dogma_paired_bc_transfer    --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=6 py run.py --task dogma_single_full_transfer  --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=7 py run.py --task dogma_single_transfer       --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=0 py run.py --task dogma_single_atac_transfer  --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=1 py run.py --task dogma_single_rna_transfer   --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=2 py run.py --task dogma_single_adt_transfer   --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=0 py run.py --task dogma_paired_a_transfer     --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=1 py run.py --task dogma_paired_b_transfer     --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=2 py run.py --task dogma_paired_c_transfer     --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
```

### Inferring latent variables, and generating imputed and batch corrected counts

```bash
tasks=(full paired_full paired_abc paired_ab paired_ac paired_bc single_full single single_atac single_rna single_adt paired_a paired_b paired_c)
for i in "${!tasks[@]}"; do
    CUDA_VISIBLE_DEVICES=$((i % 8)) py run.py --task "dogma_"${tasks[$i]}"_transfer" --ref atlas_no_dogma --act predict_all_latent_bc --init_model sp_00003699 &
done
```

### Evaluation

UMAP visualization of the joint embeddings generated by MIDAS in mosaic integration:

```bash
data=dogma
tasks=(full paired_full single_full paired_abc paired_ab paired_ac paired_bc single paired_a paired_b paired_c single_atac single_rna single_adt)
for task in "${tasks[@]}"; do
    Rscript comparison/midas_embed.r --exp e0 --init_model sp_00003699 --task $data"_"$task"_transfer" &
done
```

UMAP visualization of the modality embeddings and joint embeddings generated by MIDAS in mosaic integration:

```bash
data=dogma
tasks=(full paired_full paired_abc paired_ab paired_ac paired_bc single_full single paired_a paired_b paired_c single_atac single_rna single_adt)
for task in "${tasks[@]}"; do
    Rscript comparison/vis_z_mosaic_split.r --init_model sp_00003699 --task $data"_"$task"_transfer" &
done
```

Computing batch correction and biological conservation metrics of MIDAS on feature space (required by scMIB):

```bash
# generate wnn graph for evaluation
data=dogma
tasks=(full paired_full paired_abc paired_ab paired_ac paired_bc single_full single)
inits=(sp_00003699)
for init in "${inits[@]}"; do
    for i in "${!tasks[@]}"; do
        Rscript comparison/midas_feat+wnn.r --exp e0 --task $data"_"${tasks[$i]}"_transfer" --init_model $init &
    done
done
```

```bash
# evaluate using wnn graph
for init in "${inits[@]}"; do
    for i in "${!tasks[@]}"; do
        py eval/benchmark_batch_bio.py --method midas_feat+wnn --exp e0 --task $data"_"${tasks[$i]}"_transfer" --init_model $init &
    done
done
```

Computing batch correction and biological conservation metrics of MIDAS on embedding space (required by scIB and scMIB):

```bash
data=dogma
tasks=(full paired_full paired_abc paired_ab paired_ac paired_bc single_full single single_atac single_rna single_adt paired_a paired_b paired_c)
inits=(sp_00003699)
for init in "${inits[@]}"; do
    for i in "${!tasks[@]}"; do
        py eval/benchmark_batch_bio.py --method midas_embed --exp e0 --task $data"_"${tasks[$i]}"_transfer" --init_model $init &
    done
done
```

Computing modality alignment metrics of MIDAS (required by scMIB):

```bash
# enerate ground-truth dogma_full datasets for scMIB modality alignment evaluation, which use the features of mosaic datasets
Rscript preprocess/combine_unseen.R --reference dogma_full_transfer        --task dogma_full_ref_full_transfer        && py preprocess/split_mat.py --task dogma_full_ref_full_transfer        &
Rscript preprocess/combine_unseen.R --reference dogma_paired_full_transfer --task dogma_full_ref_paired_full_transfer && py preprocess/split_mat.py --task dogma_full_ref_paired_full_transfer &
Rscript preprocess/combine_unseen.R --reference dogma_single_full_transfer --task dogma_full_ref_single_full_transfer && py preprocess/split_mat.py --task dogma_full_ref_single_full_transfer &
Rscript preprocess/combine_unseen.R --reference dogma_paired_abc_transfer  --task dogma_full_ref_paired_abc_transfer  && py preprocess/split_mat.py --task dogma_full_ref_paired_abc_transfer  &
Rscript preprocess/combine_unseen.R --reference dogma_paired_ab_transfer   --task dogma_full_ref_paired_ab_transfer   && py preprocess/split_mat.py --task dogma_full_ref_paired_ab_transfer   &
Rscript preprocess/combine_unseen.R --reference dogma_paired_ac_transfer   --task dogma_full_ref_paired_ac_transfer   && py preprocess/split_mat.py --task dogma_full_ref_paired_ac_transfer   &
Rscript preprocess/combine_unseen.R --reference dogma_paired_bc_transfer   --task dogma_full_ref_paired_bc_transfer   && py preprocess/split_mat.py --task dogma_full_ref_paired_bc_transfer   &
Rscript preprocess/combine_unseen.R --reference dogma_single_transfer      --task dogma_full_ref_single_transfer      && py preprocess/split_mat.py --task dogma_full_ref_single_transfer      &
```

```bash
# perform modality translation
data=dogma
tasks=(full paired_full paired_abc paired_ab paired_ac paired_bc single_full single)
inits=(sp_00003699)
act=translate
for init in "${inits[@]}"; do
    for i in "${!tasks[@]}"; do
        CUDA_VISIBLE_DEVICES=$i py run.py --task $data"_full_ref_"${tasks[$i]}"_transfer" --ref $data"_"${tasks[$i]}"_transfer" --act $act --init_model $init --init_from_ref 1 --exp e0 &
    done
done
```

```bash
# compute metrics
for init in "${inits[@]}"; do
    for i in "${!tasks[@]}"; do
        py eval/benchmark_mod.py --method midas_embed --exp e0 --task ${data}_${tasks[$i]}_transfer --init_model $init
    done
done
```

scMIB comparison of MIDAS mosaic integration results:

```bash
tasks="dogma_full_transfer dogma_paired_full_transfer dogma_paired_abc_transfer dogma_paired_ab_transfer dogma_paired_ac_transfer dogma_paired_bc_transfer dogma_single_full_transfer dogma_single_transfer"
py eval/combine_metrics_scmib.py --tasks $tasks --init_model sp_00003699
```

scIB comparison of MIDAS mosaic integration results and SOTA rectangular integration results:

```bash
tasks="dogma_full_transfer dogma_paired_full_transfer dogma_paired_abc_transfer dogma_paired_ab_transfer dogma_paired_ac_transfer dogma_paired_bc_transfer dogma_single_full_transfer dogma_single_transfer
       dogma_single_atac_transfer dogma_single_rna_transfer dogma_single_adt_transfer dogma_paired_a_transfer dogma_paired_b_transfer dogma_paired_c_transfer"
py eval/combine_metrics_scib.py --tasks $tasks --init_model sp_00003699 --mosaic 1 --sota 1
```

## Label transfer

### Reciprocal reference mapping for query dogma mosaic datasets

```bash
CUDA_VISIBLE_DEVICES=0 py run.py --task dogma_full_continual        --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=1 py run.py --task dogma_paired_full_continual --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=2 py run.py --task dogma_paired_abc_continual  --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=0 py run.py --task dogma_paired_ab_continual   --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=1 py run.py --task dogma_paired_ac_continual   --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=2 py run.py --task dogma_paired_bc_continual   --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=0 py run.py --task dogma_single_full_continual --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=1 py run.py --task dogma_single_continual      --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=2 py run.py --task dogma_single_atac_continual --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=0 py run.py --task dogma_single_rna_continual  --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=1 py run.py --task dogma_single_adt_continual  --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=0 py run.py --task dogma_paired_a_continual    --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=1 py run.py --task dogma_paired_b_continual    --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
CUDA_VISIBLE_DEVICES=2 py run.py --task dogma_paired_c_continual    --ref atlas_no_dogma --init_model sp_latest --init_from_ref 1 --epoch 4000 &
```

### Inferring latent variables, and generating imputed and batch corrected counts

```bash
tasks=(full paired_full paired_abc paired_ab paired_ac paired_bc single_full single single_atac single_rna single_adt paired_a paired_b paired_c)
for i in "${!tasks[@]}"; do
    CUDA_VISIBLE_DEVICES=$((i % 8)) py run.py --task "dogma_"${tasks[$i]}"_continual" --ref atlas_no_dogma --act predict_joint --init_model sp_00003799 &
done
```

### Evaluation

Performing query-to-reference mapping for comparison:

```bash
tasks=(full paired_full paired_abc paired_ab paired_ac paired_bc single_full single single_atac single_rna single_adt paired_a paired_b paired_c)
for i in "${!tasks[@]}"; do
    CUDA_VISIBLE_DEVICES=$i py run.py --task "dogma_"${tasks[$i]}"_transfer" --ref atlas_no_dogma --init_from_ref 1 --act predict_joint --init_model sp_00001999 --drop_s 1 &
done
```

Computing the micro F1-scores of different mapping strategies:

```bash
# query-to-reference mapping
for i in "${!tasks[@]}"; do
    py eval/benchmark_transfer.py --task "dogma_"${tasks[$i]}"_transfer" --init_model sp_00001999 &
done
```

```bash
# reference-to-query mapping
for i in "${!tasks[@]}"; do
    py eval/benchmark_transfer.py --task "dogma_"${tasks[$i]}"_transfer" --init_model sp_00003699 &
done
```

```bash
# reciprocal reference mapping
for i in "${!tasks[@]}"; do
    py eval/benchmark_continual.py --task "dogma_"${tasks[$i]}"_continual" --ref atlas_no_dogma --init_model sp_00003799 &
done
```
