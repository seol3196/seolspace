<template>
  <div>
    <div id="loginSection" v-if="!isLoggedIn">
      <div class="login-container">
        <h1 class="login-title">SEOL'space</h1>
        <form class="password-form" @submit.prevent="checkPassword">
          <input 
            type="password" 
            v-model="password" 
            class="password-input" 
            placeholder="비밀번호를 입력하세요"
            maxlength="4"
            required
          >
          <button type="submit" class="submit-button">입력</button>
          <div v-if="showError" class="error-message">
            비밀번호가 올바르지 않습니다
          </div>
        </form>
      </div>
    </div>

    <div id="dashboardSection" v-if="isLoggedIn">
      <div class="container">
        <header>
          <h1 class="title">SEOL'space</h1>
          <p class="subtitle">rmsid</p>
        </header>

        <div class="tools-grid">
          <a href="calendar.html" style="text-decoration: none;">
            <div class="tool-card">
              <div class="tool-icon">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
                </svg>
              </div>
              <h3 class="tool-title">출장 일정표</h3>
              <p class="tool-description">출장 일정을 캘린더 형식으로 관리하고 조회할 수 있습니다.</p>
              <span class="tool-status status-active">사용 가능</span>
            </div>
          </a>

          <a href="youtube.html" style="text-decoration: none;">
            <div class="tool-card">
              <div class="tool-icon">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14.752 11.168l-3.197-2.132A1 1 0 0010 9.87v4.263a1 1 0 001.555.832l3.197-2.132a1 1 0 000-1.664z" />
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              </div>
              <h3 class="tool-title">유튜브 변환기</h3>
              <p class="tool-description">유튜브 링크를 프라이버시 보호 모드로 변환합니다.</p>
              <span class="tool-status status-active">사용 가능</span>
            </div>
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      password: '',
      correctPassword: '3196',
      showError: false,
      isLoggedIn: false
    };
  },
  methods: {
    checkPassword() {
      if (this.password === this.correctPassword) {
        localStorage.setItem('isLoggedIn', 'true');
        this.isLoggedIn = true;
      } else {
        this.showError = true;
        this.password = '';
      }
    }
  },
  mounted() {
    this.isLoggedIn = localStorage.getItem('isLoggedIn') === 'true';
  }
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, system-ui, Roboto, sans-serif;
  background-color: #f8fafc;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.login-container {
  background: white;
  padding: 2rem;
  border-radius: 1rem;
  box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
  width: 90%;
  max-width: 400px;
  text-align: center;
}

.login-title {
  color: #2563eb;
  font-size: 1.5rem;
  margin-bottom: 1.5rem;
}

.password-form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.password-input {
  padding: 0.75rem;
  border: 2px solid #e2e8f0;
  border-radius: 0.5rem;
  font-size: 1rem;
  text-align: center;
  letter-spacing: 0.2em;
  transition: border-color 0.2s;
}

.password-input:focus {
  outline: none;
  border-color: #2563eb;
}

.submit-button {
  background: #2563eb;
  color: white;
  border: none;
  padding: 0.75rem;
  border-radius: 0.5rem;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.2s;
}

.submit-button:hover {
  background: #1d4ed8;
}

.error-message {
  color: #dc2626;
  font-size: 0.875rem;
  margin-top: 0.5rem;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
}

header {
  text-align: center;
  margin-bottom: 3rem;
  padding: 2rem 0;
}

.title {
  font-size: 2.5rem;
  color: #2563eb;
  margin-bottom: 1rem;
}

.subtitle {
  color: #64748b;
  font-size: 1.1rem;
}

.tools-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 2rem;
}

.tool-card {
  background: white;
  border-radius: 1rem;
  padding: 1.5rem;
  box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
  transition: transform 0.2s, box-shadow 0.2s;
  cursor: pointer;
}

.tool-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 20px -8px rgb(0 0 0 / 0.15);
}

.tool-icon {
  background: #e0e7ff;
  width: 48px;
  height: 48px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1rem;
}

.tool-title {
  font-size: 1.25rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.tool-description {
  color: #64748b;
  font-size: 0.95rem;
}

.tool-status {
  margin-top: 1rem;
  font-size: 0.875rem;
  padding: 0.25rem 0.75rem;
  border-radius: 1rem;
  display: inline-block;
}

.status-active {
  background-color: #dcfce7;
  color: #166534;
}

.status-coming {
  background-color: #fef9c3;
  color: #854d0e;
}
</style>