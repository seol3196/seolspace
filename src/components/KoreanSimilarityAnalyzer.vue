<template>
  <div>
    <div style="
      background-color: #2563eb; 
      padding: 1rem; 
      margin-bottom: 20px;
      display: flex;
      align-items: center;
    ">
      <a href="/" style="
        color: white;
        text-decoration: none;
        display: flex;
        align-items: center;
        gap: 8px;
      ">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"/>
        </svg>
        <span>홈으로</span>
      </a>
    </div>

    <div class="analyzer-container">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">
            <i class="fas fa-file-text"></i>
            한글 문장 유사도 분석기 (유사도 0.9 이상)
          </h2>
        </div>
        <div class="card-content">
          <div class="input-section">
            <label class="input-label">분석할 문장들을 입력하세요 (한 줄에 하나의 문장)</label>
            <textarea
              v-model="inputText"
              rows="8"
              class="input-textarea"
              placeholder="예시:
  오늘 날씨가 매우 좋습니다
  날씨가 정말 좋네요
  학교에 갑니다"
            ></textarea>
          </div>
  
          <button
            @click="analyzeSimilarity"
            :disabled="loading || !inputText.trim()"
            class="analyze-button"
            :class="{ 'button-disabled': loading || !inputText.trim() }"
          >
            {{ loading ? '분석 중...' : '유사도 분석하기' }}
          </button>
  
          <div v-if="results.length > 0" class="results-section">
            <h3 class="results-title">분석 결과</h3>
            <div v-for="(result, idx) in results" :key="idx" class="result-card">
              <div class="result-content">
                <div class="original-sentence">
                  <span class="sentence-label">문장 {{ result.originalIndex }}:</span>
                  <div class="sentence-text">{{ result.originalSentence }}</div>
                </div>
                <div class="similar-sentences">
                  <div class="similar-label">유사한 문장들:</div>
                  <div
                    v-for="(similar, sidx) in result.similarSentences"
                    :key="sidx"
                    class="similar-item"
                  >
                    <div class="similar-header">
                      <span class="similar-index">문장 {{ similar.index }}</span>
                      <span class="similarity-score">
                        (유사도: {{ similar.similarity.toFixed(3) }})
                      </span>
                    </div>
                    <div class="similar-text">{{ similar.sentence }}</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
  
          <div v-else-if="inputText.trim() && !loading" class="no-results">
            <i class="fas fa-exclamation-circle"></i>
            <p>유사도 0.9 이상인 문장이 없습니다.</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
  
  <script>
  export default {
    name: 'KoreanSimilarityAnalyzer',
    data() {
      return {
        results: [],
        inputText: '',
        loading: false
      }
    },
    methods: {
      tokenize(text) {
        return text
          .split(/[\s,.!?()[\]{}'"]+/)
          .filter(word => word.length > 0);
      },
  
      sentenceToVector(sentence, vocabulary) {
        const vector = new Array(vocabulary.size).fill(0);
        const words = this.tokenize(sentence);
        
        words.forEach(word => {
          const index = [...vocabulary].indexOf(word);
          if (index !== -1) {
            vector[index]++;
          }
        });
        
        return vector;
      },
  
      calculateCosineSimilarity(vec1, vec2) {
        const dotProduct = vec1.reduce((sum, a, i) => sum + a * vec2[i], 0);
        const magnitude1 = Math.sqrt(vec1.reduce((sum, a) => sum + a * a, 0));
        const magnitude2 = Math.sqrt(vec2.reduce((sum, b) => sum + b * b, 0));
        
        if (magnitude1 === 0 || magnitude2 === 0) return 0;
        return dotProduct / (magnitude1 * magnitude2);
      },
  
      analyzeSimilarity() {
        this.loading = true;
        try {
          const sentences = this.inputText
            .split('\n')
            .map(s => s.trim())
            .filter(s => s.length > 0);
  
          const vocabulary = new Set();
          sentences.forEach(sentence => {
            this.tokenize(sentence).forEach(word => vocabulary.add(word));
          });
  
          const vectors = sentences.map(sentence => 
            this.sentenceToVector(sentence, vocabulary)
          );
  
          const similarityResults = [];
  
          for (let i = 0; i < sentences.length; i++) {
            const similarSentences = [];
            
            for (let j = 0; j < sentences.length; j++) {
              if (i !== j) {
                const similarity = this.calculateCosineSimilarity(vectors[i], vectors[j]);
                
                if (similarity >= 0.9) {
                  similarSentences.push({
                    sentence: sentences[j],
                    similarity: similarity,
                    index: j + 1
                  });
                }
              }
            }
  
            if (similarSentences.length > 0) {
              similarityResults.push({
                originalSentence: sentences[i],
                originalIndex: i + 1,
                similarSentences: similarSentences.sort((a, b) => b.similarity - a.similarity)
              });
            }
          }
  
          this.results = similarityResults;
        } catch (error) {
          console.error('분석 중 오류 발생:', error);
        }
        this.loading = false;
      }
    }
  }
  </script>
  
  <style scoped>
  .analyzer-container {
    width: 100%;
    max-width: 64rem;
    margin: 0 auto;
    padding: 1rem;
  }
  
  .card {
    background: white;
    border-radius: 0.5rem;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  .card-header {
    padding: 1rem;
    border-bottom: 1px solid #e5e7eb;
  }
  
  .card-title {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 1.25rem;
    font-weight: 500;
  }
  
  .card-content {
    padding: 1.5rem;
  }
  
  .input-section {
    margin-bottom: 1.5rem;
  }
  
  .input-label {
    display: block;
    font-size: 0.875rem;
    font-weight: 500;
    margin-bottom: 0.5rem;
  }
  
  .input-textarea {
    width: 100%;
    padding: 0.75rem;
    border: 1px solid #e5e7eb;
    border-radius: 0.375rem;
    resize: vertical;
  }
  
  .analyze-button {
    width: 100%;
    padding: 0.5rem 0;
    background-color: #3b82f6;
    color: white;
    border: none;
    border-radius: 0.375rem;
    cursor: pointer;
  }
  
  .analyze-button:hover {
    background-color: #2563eb;
  }
  
  .button-disabled {
    background-color: #d1d5db;
    cursor: not-allowed;
  }
  
  .results-section {
    margin-top: 1.5rem;
  }
  
  .results-title {
    font-size: 1.125rem;
    font-weight: 500;
    margin-bottom: 1rem;
  }
  
  .result-card {
    background: white;
    border: 1px solid #e5e7eb;
    border-radius: 0.375rem;
    padding: 1rem;
    margin-bottom: 1rem;
  }
  
  .result-content {
    display: flex;
    flex-direction: column;
    gap: 0.75rem;
  }
  
  .sentence-label {
    font-weight: 500;
  }
  
  .sentence-text {
    background-color: #f9fafb;
    padding: 0.5rem;
    border-radius: 0.25rem;
    margin-top: 0.25rem;
  }
  
  .similar-sentences {
    padding-left: 1rem;
  }
  
  .similar-label {
    font-size: 0.875rem;
    color: #4b5563;
    margin-bottom: 0.5rem;
  }
  
  .similar-item {
    background-color: #eff6ff;
    padding: 0.5rem;
    border-radius: 0.25rem;
    margin-bottom: 0.5rem;
  }
  
  .similar-header {
    font-size: 0.875rem;
  }
  
  .similar-index {
    font-weight: 500;
  }
  
  .similarity-score {
    color: #6b7280;
    margin-left: 0.5rem;
  }
  
  .similar-text {
    margin-top: 0.25rem;
  }
  
  .no-results {
    text-align: center;
    padding: 1rem;
    background-color: #f9fafb;
    border-radius: 0.375rem;
    color: #6b7280;
  }
  
  a {
    color: white;
    text-decoration: none;
  }
  
  a:hover {
    opacity: 0.9;
  }
  </style>