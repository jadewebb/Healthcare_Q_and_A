# Healthcare Q&A

Fine-tuning Llama 3.1 using QLoRA to perform healthcare Q&A

# Dataset

MedQuAD (Medical Question Answering Dataset)
 
   - Kaggle Source: https://www.kaggle.com/datasets/jpmiller/layoutlm

   - Original Paper: A. Ben Abacha and D. Demner-Fushman, "A question-entailment approach to question answering," BMC Bioinformatics, vol. 20, Oct. 2019, Art. no. 511, arXiv:1901.08079

   - 16412 samples of consumer medical question-answer pairs about diseases, drugs, and other medical entities, extracted from 12 trusted medical websites
   
      - Subset of the original 47457 sample MedQuAD dataset, as samples under the MedlinePlus copyright were removed
    
# Preprocessing

Dataset was loaded, inspected, and cleaned down to 16345 samples, then saved as a Python Dataset

Dataset was shuffled and split into training, validation, and testing sets (80:10:10)

Question-answer pairs were mapped to the proper "messages" format for compatability with Llama 3.1

   ```
      "messages": [
          {"role": "user", "content": <question>},
          {"role": "assistant", "content": <answer>}
      ]
   ```

# Llama 3.1 Fine-Tuning using QLoRA





