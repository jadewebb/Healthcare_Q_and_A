# Healthcare Q&A

Fine-tuning Llama 3.1 using QLoRA to perform healthcare Q&A

## Dataset

MedQuAD (Medical Question Answering Dataset)
 
   - Kaggle Source: https://www.kaggle.com/datasets/jpmiller/layoutlm

   - Original Paper: A. Ben Abacha and D. Demner-Fushman, "A question-entailment approach to question answering," BMC Bioinformatics, vol. 20, Oct. 2019, Art. no. 511, arXiv:1901.08079

   - 16412 samples of consumer medical question-answer pairs about diseases, drugs, and other medical entities, extracted from 12 trusted medical websites
   
      - Subset of the original 47457 sample MedQuAD dataset, as samples under the MedlinePlus copyright were removed
    
## Preprocessing

Dataset was loaded, inspected, and cleaned down to 16345 samples, then saved as a Python Dataset

Dataset was shuffled and split into training, validation, and testing sets (80:10:10)

Question-answer pairs were mapped to the proper conversation format for compatability with Llama 3.1

   ```
      "messages": [
          {"role": "user", "content": <question>},
          {"role": "assistant", "content": <answer>}
      ]
   ```

## Llama 3.1 Fine-Tuning using QLoRA

**Llama 3.1 8B Instruct** tokenizer and pretrained model were loaded with **4-bit normal float quantization** using transformers

**QLoRA PEFT configuration** and **SFT trainer** hyperparameters were tuned for optimal fine-tuning efficiency, memory usage, and metric accuracy

   - **Key Considerations**

       - **General Considerations**: stable α=2r LoRA scaling, NF4 quantization

       - **Llama 3.1 Archiecture**: full linear module targeting, custom training-compatible chat template
         
       - **NVIDIA A100 GPU**: optimized 4x4=16 global batch size, native bfloat16 hardware acceleration, full context learning with truncation, paged 8-bit optimization
         
       - **Q&A Structure**: masked instruction loss calculation, sequence isolation

       - **Experiment Monitoring**: real-time TensorBoard telemetry, automated step-wise validation and checkpointing, early stopping

Fine-tuning was terminated after 200 steps due to validation loss stabilization, with the final metrics being:

Step | Training Loss | Validation Loss | Entropy | Mean Token Accuracy
--- | --- | --- | --- | ---
200 | 1.009682 | 0.9677 | 0.96504 | 0.748658

Visualizations were created for Training and Validation Loss, Entropy, and Accuracy

<p align="center">
<img src="https://raw.githubusercontent.com/jadewebb/Healthcare_Q_and_A/main/log_metrics.png" width="700"></p>

## Evaluation

A **batched generation loop** was used to generate answers for all test set questions, which were evaluated through comparison to ground-truth answers using **BLEU** and **ROUGE** metrics

Metric | Score
--- | ---
BLEU | 0.1507
ROUGE-1 | 0.3371
ROUGE-2 | 0.1459
ROUGE-L: 0.2088

Performance demonstrates that the model successfully generates coherent, **multi-word medical phrasing** that aligns with professional, consumer-aligned clinical answers. Further experimentation is necessary to **improve answer quality**, including adherence to the dataset answer style, structural completion, web artifact pollution, and premature truncation. This includes but is not limited to stronger LLMs, more compute power, parameter expansion, RAG integration, and an automatic RLAIF evaluation pipeline.

