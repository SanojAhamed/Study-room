<script setup>
import { computed, nextTick, ref } from 'vue'
import DOMPurify from 'dompurify'
import { marked } from 'marked'

const lessons = {
  JavaScript: {
    icon: '</>',
    level: 'BEGINNER',
    title: 'JavaScript basics',
    introduction: 'JavaScript gives a webpage the ability to respond when someone uses it.',
    analogy: 'Think of a webpage like a house: HTML is the structure, CSS is the decoration, and JavaScript is the electricity that makes things happen.',
    exampleLabel: 'A variable is a named place to keep a value:',
    example: "const learner = 'Sanoj'\nconsole.log(`Hello, ${learner}!`)",
    takeaway: 'Use const for values that should not be reassigned. Use let when the value needs to change.',
    quiz: [
      {
        question: 'Which keyword is the best default for a variable you will not reassign?',
        options: ['const', 'let', 'repeat'],
        answer: 'const',
        explanation: 'Use const when the variable binding will not be reassigned.'
      },
      {
        question: 'Which keyword should you use when a variable needs to be reassigned?',
        options: ['const', 'let', 'static'],
        answer: 'let',
        explanation: 'let allows a variable to be assigned a new value.'
      },
      {
        question: "What does console.log('Hello') do?",
        options: ['Prints Hello to the console', 'Creates a variable named Hello', 'Changes the page title'],
        answer: 'Prints Hello to the console',
        explanation: 'console.log writes the supplied value to the browser or runtime console.'
      },
      {
        question: 'Which is a valid JavaScript string?',
        options: ['"Good morning"', '<p>Good morning</p>', '{Good morning}'],
        answer: '"Good morning"',
        explanation: 'Text strings are written inside matching quotes, such as single or double quotes.'
      },
      {
        question: 'What is logged after let score = 2; score = score + 1; console.log(score)?',
        options: ['2', '3', 'score + 1'],
        answer: '3',
        explanation: 'score starts at 2, then 1 is added and assigned back, so its new value is 3.'
      }
    ],
    practice: {
      task: 'Create a const variable named favoriteColor, give it a color as a string, and print it with console.log.',
      starter: "const favoriteColor = 'blue'\nconsole.log(favoriteColor)",
      hint: 'Put your text inside quotes, then pass the variable name to console.log.'
    }
  },
  HTML: {
    icon: '⌘',
    level: 'BEGINNER',
    title: 'HTML page structure',
    introduction: 'HTML describes the meaning and structure of the content on a webpage.',
    analogy: 'HTML is like the frame of a book: headings label sections, paragraphs hold the writing, and links connect you to other pages.',
    exampleLabel: 'Use elements that describe their content:',
    example: '<main>\n  <h1>My first page</h1>\n  <p>Learning one step at a time.</p>\n</main>',
    takeaway: 'Choose an HTML element for what its content means, not just for how you want it to look.',
    quiz: [
      {
        question: 'Which element is intended for the main content of a page?',
        options: ['<main>', '<small>', '<br>'],
        answer: '<main>',
        explanation: '<main> identifies the central content of the page.'
      },
      {
        question: 'Which element creates the most important page heading?',
        options: ['<h1>', '<p>', '<title>'],
        answer: '<h1>',
        explanation: '<h1> is the top-level heading in the visible page content.'
      },
      {
        question: 'Which element creates a link to another page?',
        options: ['<a>', '<linktext>', '<navto>'],
        answer: '<a>',
        explanation: 'The anchor element <a> creates a link, usually with an href attribute.'
      },
      {
        question: 'What should an image’s alt text provide?',
        options: ['A text alternative describing its purpose', 'The image file size', 'A CSS color'],
        answer: 'A text alternative describing its purpose',
        explanation: 'Alt text communicates an image’s meaningful content or purpose to people who cannot see it.'
      },
      {
        question: 'Which element is most appropriate for a paragraph of text?',
        options: ['<p>', '<section-title>', '<img>'],
        answer: '<p>',
        explanation: '<p> represents a paragraph of text.'
      }
    ],
    practice: {
      task: 'Write a short page section with one heading and one paragraph about something you are learning.',
      starter: '<section>\n  <h2>What I am learning</h2>\n  <p>Write your sentence here.</p>\n</section>',
      hint: 'Put the heading and paragraph inside the section element.'
    }
  },
  CSS: {
    icon: '✳',
    level: 'BEGINNER',
    title: 'CSS selectors and styles',
    introduction: 'CSS controls how HTML content looks, from colors and spacing to page layout.',
    analogy: 'If HTML names the pieces of a room, CSS is the set of decorating instructions that tells you how each piece should look.',
    exampleLabel: 'A selector chooses elements, and declarations set their styles:',
    example: 'p {\n  color: navy;\n  font-size: 1.1rem;\n}',
    takeaway: 'A CSS rule has a selector followed by declarations inside braces. Each declaration is a property and a value.',
    quiz: [
      {
        question: 'Which selector targets elements with class="card"?',
        options: ['.card', '#card', 'card()'],
        answer: '.card',
        explanation: 'A dot followed by the class name selects elements with that class.'
      },
      {
        question: 'Which CSS property changes text color?',
        options: ['color', 'font-style-color', 'text-fill'],
        answer: 'color',
        explanation: 'The color property sets the foreground color of text.'
      },
      {
        question: 'In color: navy;, what is navy?',
        options: ['The property value', 'The selector', 'The HTML element'],
        answer: 'The property value',
        explanation: 'In a CSS declaration, the part after the colon is the value.'
      },
      {
        question: 'Which unit is commonly used for a flexible relative font size?',
        options: ['rem', 'kg', 'dpi'],
        answer: 'rem',
        explanation: 'rem is relative to the root element’s font size and is useful for scalable sizing.'
      },
      {
        question: 'Which punctuation surrounds the declarations in a CSS rule?',
        options: ['Curly braces { }', 'Square brackets [ ]', 'Parentheses ( )'],
        answer: 'Curly braces { }',
        explanation: 'CSS declarations for a selector are placed inside curly braces.'
      }
    ],
    practice: {
      task: 'Write a CSS rule that makes all h2 headings green and gives them a little space below.',
      starter: 'h2 {\n  color: green;\n  margin-bottom: 1rem;\n}',
      hint: 'Use h2 as the selector, then add color and margin-bottom declarations inside braces.'
    }
  }
}

const learnerNameKey = 'study-room-learner-name'
const topic = ref('JavaScript')
const mode = ref('explain')
const showHint = ref(false)

function loadLearnerName() {
  try {
    return localStorage.getItem(learnerNameKey) || ''
  } catch (error) {
    console.warn('Could not read the saved learner name.', error)
    return ''
  }
}

const learnerName = ref(loadLearnerName())
const learnerInitials = computed(() => learnerName.value
  .split(/\s+/)
  .filter(Boolean)
  .slice(0, 2)
  .map(part => part[0].toLocaleUpperCase())
  .join('') || 'ST')
const studyRoomTitle = computed(() => learnerName.value ? `${learnerName.value}'s Study Room` : 'Study Room')
const profileSaveError = ref('')

function saveLearnerName() {
  learnerName.value = learnerName.value.trim().slice(0, 40)
  profileSaveError.value = ''

  try {
    if (learnerName.value) localStorage.setItem(learnerNameKey, learnerName.value)
    else localStorage.removeItem(learnerNameKey)
  } catch (error) {
    console.warn('Could not save the learner name.', error)
    profileSaveError.value = 'Could not save the name in this browser.'
  }
}

function loadProgress() {
  try {
    const saved = localStorage.getItem('sanoj-study-progress')
    if (!saved) return []
    const parsed = JSON.parse(saved)
    return Array.isArray(parsed)
      ? parsed.filter(item => Object.hasOwn(lessons, item))
      : []
  } catch (error) {
    console.warn('Could not read saved study progress.', error)
    return []
  }
}

const completedLessons = ref(loadProgress())
const progressSaveError = ref('')
const lesson = computed(() => lessons[topic.value])
const progress = computed(() => Math.round((completedLessons.value.length / Object.keys(lessons).length) * 100))
const tutorMessages = ref([])
const quizQuestionCount = ref(0)
const quizComplete = ref(false)
const selectedAnswer = ref('')
const quizQuestion = computed(() => lesson.value.quiz[Math.min(quizQuestionCount.value, lesson.value.quiz.length - 1)])
const quizAnswerIsCorrect = computed(() => selectedAnswer.value === quizQuestion.value.answer)
const quizAnswerCount = computed(() => quizQuestionCount.value + (selectedAnswer.value ? 1 : 0))
const quizFeedbackReady = computed(() => tutorMessages.value.at(-1)?.role === 'assistant')
const tutorInput = ref('')
const tutorLoading = ref(false)
const tutorError = ref('')
const tutorModel = ref('')
const tutorConversation = ref(null)
const failedTutorPrompt = ref('')
const failedTutorDisplayText = ref('')
const practiceAnswer = ref('')

marked.setOptions({ breaks: true, gfm: true })

const modePrompts = {
  explain: 'Use the lesson notes to explain this topic to me in a simple, memorable way. Give one small example and ask me one question to check my understanding.',
  practice: 'Create one small hands-on exercise about this topic. Give me a clear goal, a tiny starter example, and one hint without showing the full solution.'
}

function lessonContext() {
  return [
    lesson.value.introduction,
    `Analogy: ${lesson.value.analogy}`,
    `${lesson.value.exampleLabel}\n${lesson.value.example}`,
    `Key takeaway: ${lesson.value.takeaway}`,
    `Practice idea: ${lesson.value.practice.task}`
  ].join('\n\n')
}

async function askTutor(text = tutorInput.value, restoreInputOnError = true, displayText = text) {
  const prompt = text.trim()
  if (!prompt || tutorLoading.value || (mode.value === 'quiz' && quizComplete.value)) return

  tutorError.value = ''
  tutorInput.value = ''
  tutorLoading.value = true
  const history = tutorMessages.value.slice(-8)
  const lastMessage = history.at(-1)
  const retryingLastPrompt = lastMessage?.role === 'user' && lastMessage.content === displayText
  if (lastMessage?.role === 'user') {
    history.pop()
    if (!retryingLastPrompt) tutorMessages.value = tutorMessages.value.slice(0, -1)
  }
  if (!retryingLastPrompt) {
    tutorMessages.value = [...tutorMessages.value, { role: 'user', content: displayText }].slice(-10)
  }
  await scrollTutorToBottom()

  try {
    const response = await fetch('/api/study', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        topic: topic.value,
        mode: mode.value,
        learnerName: learnerName.value,
        prompt,
        context: lessonContext(),
        history
      })
    })
    const data = await response.json()

    if (!response.ok) throw new Error(data.error || 'Gemma could not answer. Please try again.')

    const responseMessage = { role: 'assistant', content: data.answer, source: data.source || 'gemma' }
    tutorMessages.value = [...tutorMessages.value, responseMessage].slice(-10)
    tutorModel.value = data.model || ''
    failedTutorPrompt.value = ''
    failedTutorDisplayText.value = ''
    await scrollTutorToBottom()
  } catch (error) {
    tutorError.value = error.message || 'Could not connect to the study tutor.'
    failedTutorPrompt.value = prompt
    failedTutorDisplayText.value = displayText
    if (restoreInputOnError) tutorInput.value = prompt
  } finally {
    tutorLoading.value = false
  }
}

function submitPracticeAnswer() {
  const answer = practiceAnswer.value.trim()
  if (!answer || tutorLoading.value) return

  askTutor(
    `My attempt for "${lesson.value.practice.task}":\n${answer}`,
    false
  )
}

function answerQuiz(option) {
  if (tutorLoading.value || selectedAnswer.value || quizComplete.value) return

  selectedAnswer.value = option
  const question = quizQuestion.value
  askTutor(
    `Please give brief, friendly feedback on my quiz answer. Question: ${question.question}\nOptions: ${question.options.join(' | ')}\nMy answer: ${option}\nCorrect answer: ${question.answer}\nExplanation: ${question.explanation}\nTell me if my choice is correct and explain why in one or two short sentences. Do not ask another question.`,
    false,
    `My answer to “${question.question}”: ${option}`
  )
}

function nextQuizQuestion() {
  if (tutorLoading.value || !quizFeedbackReady.value) return

  if (quizQuestionCount.value === lesson.value.quiz.length - 1) {
    quizQuestionCount.value = lesson.value.quiz.length
    quizComplete.value = true
    markLessonComplete()
  } else {
    quizQuestionCount.value += 1
  }

  selectedAnswer.value = ''
  tutorMessages.value = []
  tutorError.value = ''
  tutorModel.value = ''
  failedTutorPrompt.value = ''
}

async function scrollTutorToBottom() {
  await nextTick()
  if (tutorConversation.value) {
    tutorConversation.value.scrollTop = tutorConversation.value.scrollHeight
  }
}

function renderTutorMessage(content) {
  return DOMPurify.sanitize(marked.parse(content))
}

function openMode(nextMode) {
  if (tutorLoading.value) return
  mode.value = nextMode
  showHint.value = false
  tutorMessages.value = []
  tutorError.value = ''
  tutorModel.value = ''
  quizQuestionCount.value = 0
  quizComplete.value = false
  selectedAnswer.value = ''
  failedTutorPrompt.value = ''
  failedTutorDisplayText.value = ''
  practiceAnswer.value = ''
  if (nextMode !== 'quiz') askTutor(modePrompts[nextMode])
}

function selectTopic(nextTopic) {
  if (tutorLoading.value || topic.value === nextTopic) return
  topic.value = nextTopic
  showHint.value = false
  tutorMessages.value = []
  tutorError.value = ''
  tutorModel.value = ''
  quizQuestionCount.value = 0
  quizComplete.value = false
  selectedAnswer.value = ''
  failedTutorPrompt.value = ''
  failedTutorDisplayText.value = ''
  practiceAnswer.value = ''
  if (mode.value !== 'quiz') askTutor(modePrompts[mode.value])
}

function markLessonComplete() {
  if (!completedLessons.value.includes(topic.value)) {
    completedLessons.value = [...completedLessons.value, topic.value]
    try {
      localStorage.setItem('sanoj-study-progress', JSON.stringify(completedLessons.value))
      progressSaveError.value = ''
    } catch (error) {
      console.warn('Could not save study progress.', error)
      progressSaveError.value = 'Progress could not be saved in this browser.'
    }
  }
}

function resetProgress() {
  completedLessons.value = []
  try {
    localStorage.removeItem('sanoj-study-progress')
    progressSaveError.value = ''
  } catch (error) {
    console.warn('Could not reset saved study progress.', error)
    progressSaveError.value = 'Saved progress could not be cleared in this browser.'
  }
  showHint.value = false
}
</script>

<template>
  <div class="app-shell">
    <header class="topbar">
      <a class="brand" href="#" :aria-label="`${studyRoomTitle} home`">
        <div class="logo">{{ learnerInitials }}</div>
        <div>
          <strong>{{ studyRoomTitle }}</strong>
          <span>A personal place to learn</span>
        </div>
      </a>
      <button class="ghost-btn" @click="resetProgress">Reset progress</button>
    </header>

    <main class="layout">
      <aside class="sidebar">
        <section class="profile-card card">
          <div class="profile-avatar">{{ learnerInitials }}</div>
          <div class="eyebrow">LEARNER</div>
          <label class="learner-name-label" for="learner-name">Your name</label>
          <input
            id="learner-name"
            v-model="learnerName"
            class="learner-name-input"
            type="text"
            maxlength="40"
            autocomplete="name"
            placeholder="Your name (optional)"
            @change="saveLearnerName"
            @keydown.enter.prevent="$event.target.blur()"
          />
          <small class="profile-hint">Saved only in this browser. Change it anytime.</small>
          <small v-if="profileSaveError" class="profile-save-error" role="alert">{{ profileSaveError }}</small>
          <p class="muted">Small steps. Real understanding. Your pace.</p>

          <div class="progress-heading">
            <span>Learning progress</span>
            <strong>{{ progress }}%</strong>
          </div>
          <div
            class="progress-track"
            role="progressbar"
            :aria-valuenow="progress"
            aria-valuemin="0"
            aria-valuemax="100"
            aria-label="Learning progress"
          >
            <span :style="{ width: `${progress}%` }"></span>
          </div>
          <small class="progress-note">{{ completedLessons.length }} of {{ Object.keys(lessons).length }} topics completed</small>
          <small v-if="progressSaveError" class="profile-save-error" role="alert">{{ progressSaveError }}</small>
        </section>

        <section class="topic-card card">
          <div class="eyebrow">YOUR STUDY PLAN</div>
          <h2>Choose a topic</h2>
          <button
            v-for="(item, name) in lessons"
            :key="name"
            class="topic-option"
            :class="{ active: topic === name }"
            :disabled="tutorLoading"
            @click="selectTopic(name)"
          >
            <span class="topic-icon">{{ item.icon }}</span>
            <span class="topic-name">{{ name }}</span>
            <span v-if="completedLessons.includes(name)" class="done-mark" aria-label="Completed">✓</span>
            <span v-else class="topic-arrow">›</span>
          </button>
        </section>

        <div class="human-note">
          <span class="human-icon">✎</span>
          <div>
            <strong>Made for real learning</strong>
            <small>Human-written lesson notes, with Gemma as your AI tutor.</small>
          </div>
        </div>
      </aside>

      <section class="lesson-card card">
        <div class="lesson-header">
          <div>
            <div class="eyebrow">{{ lesson.level }} · {{ topic }}</div>
            <h2>{{ lesson.title }}</h2>
          </div>
          <span class="lesson-number">PERSONAL STUDY GUIDE</span>
        </div>

        <nav class="mode-tabs" aria-label="Lesson section">
          <button :class="{ active: mode === 'explain' }" :disabled="tutorLoading" @click="openMode('explain')">Learn with Gemma</button>
          <button :class="{ active: mode === 'quiz' }" :disabled="tutorLoading" @click="openMode('quiz')">5-question quiz</button>
          <button :class="{ active: mode === 'practice' }" :disabled="tutorLoading" @click="openMode('practice')">Practice</button>
        </nav>

        <article v-if="mode === 'explain'" class="lesson-content">
          <div class="section-kicker"><span>01</span> THE IDEA</div>
          <h3>{{ lesson.introduction }}</h3>
          <div class="analogy-box">
            <span class="box-icon">A</span>
            <div>
              <strong>A simple way to picture it</strong>
              <p>{{ lesson.analogy }}</p>
            </div>
          </div>
          <div class="example-section">
            <div class="section-kicker"><span>02</span> SEE IT IN CODE</div>
            <p>{{ lesson.exampleLabel }}</p>
            <pre><code>{{ lesson.example }}</code></pre>
          </div>
          <div class="takeaway-box">
            <strong>Keep in mind</strong>
            <p>{{ lesson.takeaway }}</p>
          </div>
          <button class="primary-btn" :disabled="tutorLoading" @click="openMode('quiz')">I understand — check me →</button>
        </article>

        <article v-else-if="mode === 'quiz'" class="lesson-content quiz-content">
          <div class="section-kicker"><span>03</span> FIVE-QUESTION QUIZ · GEMMA FEEDBACK</div>
          <h3>{{ quizComplete ? 'Quiz complete — great work!' : `Question ${quizQuestionCount + 1} of ${lesson.quiz.length}` }}</h3>
          <div class="quiz-progress" role="progressbar" :aria-valuenow="quizAnswerCount" aria-valuemin="0" :aria-valuemax="lesson.quiz.length" aria-label="Quiz progress">
            <span :style="{ width: `${quizAnswerCount / lesson.quiz.length * 100}%` }"></span>
          </div>
          <template v-if="!quizComplete">
            <p class="quiz-question">{{ quizQuestion.question }}</p>
            <div class="answer-list">
              <button
                v-for="option in quizQuestion.options"
                :key="option"
                class="answer-option"
                :class="{
                  selected: selectedAnswer === option,
                  correct: selectedAnswer === option && quizAnswerIsCorrect,
                  incorrect: selectedAnswer === option && !quizAnswerIsCorrect
                }"
                :disabled="Boolean(selectedAnswer) || tutorLoading"
                @click="answerQuiz(option)"
              >
                <span class="answer-radio"></span>
                {{ option }}
                <span v-if="selectedAnswer === option && quizAnswerIsCorrect" class="answer-result">✓</span>
              </button>
            </div>
            <div v-if="selectedAnswer" class="feedback" :class="{ positive: quizAnswerIsCorrect }" role="status">
              <strong>{{ quizAnswerIsCorrect ? 'That’s right!' : `Not quite. The answer is ${quizQuestion.answer}.` }}</strong>
              <p>{{ quizQuestion.explanation }}</p>
            </div>
            <button
              v-if="selectedAnswer && quizFeedbackReady && !tutorError"
              class="primary-btn"
              @click="nextQuizQuestion"
            >
              {{ quizQuestionCount === lesson.quiz.length - 1 ? 'Finish quiz ✓' : 'Next question →' }}
            </button>
          </template>
          <div v-else class="quiz-completion">
            <strong>All {{ lesson.quiz.length }} questions complete.</strong>
            <p>Gemma gave feedback on each answer. Your progress is saved in this browser.</p>
            <button class="primary-btn" @click="openMode('practice')">Try a practice exercise →</button>
            <button class="text-btn" @click="openMode('quiz')">Take the quiz again</button>
          </div>
        </article>

        <article v-else class="lesson-content practice-content">
          <div class="section-kicker"><span>04</span> HANDS-ON PRACTICE</div>
          <h3>Try it yourself</h3>
          <p class="practice-task">{{ lesson.practice.task }}</p>
          <div class="practice-workspace">
            <div class="workspace-heading"><span></span><span></span><span></span><small>STARTER EXAMPLE</small></div>
            <pre><code>{{ lesson.practice.starter }}</code></pre>
          </div>
          <button class="hint-btn" @click="showHint = !showHint">
            {{ showHint ? 'Hide hint' : 'Need a hint?' }}
          </button>
          <div v-if="showHint" class="hint-box">{{ lesson.practice.hint }}</div>
          <label class="practice-answer-label" for="practice-answer">Your attempt</label>
          <textarea
            id="practice-answer"
            v-model="practiceAnswer"
            class="practice-answer"
            rows="5"
            maxlength="1400"
            :disabled="tutorLoading"
            placeholder="Write your code or explain your approach here..."
          ></textarea>
          <div class="practice-submit-row">
            <small>Your attempt is sent to Gemma for feedback.</small>
            <button
              class="primary-btn"
              :disabled="tutorLoading || !practiceAnswer.trim()"
              @click="submitPracticeAnswer"
            >
              {{ tutorLoading ? 'Reviewing…' : 'Get Gemma feedback →' }}
            </button>
          </div>
          <button class="primary-btn" :disabled="tutorLoading" @click="markLessonComplete(); openMode('explain')">
            Mark topic complete ✓
          </button>
        </article>

        <section class="tutor-panel" aria-label="Gemma tutor">
          <div class="tutor-heading">
            <div>
              <div class="eyebrow">YOUR OPEN-WEIGHT STUDY TUTOR</div>
              <h3>Learn together with Gemma</h3>
            </div>
            <span class="model-chip">✦ Gemma 3</span>
          </div>
          <p class="tutor-intro">The lessons and quiz questions are human-written. Gemma explains {{ topic }}, gives personal feedback on your quiz answers, and reviews your practice.</p>

          <div v-if="mode !== 'quiz' && !tutorMessages.length && !tutorLoading" class="tutor-start-actions">
            <button :disabled="tutorLoading" @click="openMode('explain')">Ask Gemma to explain</button>
            <button :disabled="tutorLoading" @click="openMode('quiz')">Start a 5-question quiz</button>
            <button :disabled="tutorLoading" @click="openMode('practice')">Get a Gemma exercise</button>
          </div>

          <div v-if="tutorMessages.length" ref="tutorConversation" class="tutor-conversation" aria-live="polite" aria-relevant="additions">
            <div
              v-for="(message, index) in tutorMessages"
              :key="index"
              class="tutor-message"
              :class="message.role"
            >
              <strong>{{ message.role === 'assistant' ? (message.source === 'greeting' ? 'Study Buddy' : 'Gemma') : 'You' }}</strong>
              <div v-if="message.role === 'assistant'" class="tutor-markdown" v-html="renderTutorMessage(message.content)"></div>
              <p v-else>{{ message.content }}</p>
            </div>
          </div>

          <div v-if="tutorLoading" class="tutor-loading" role="status">Gemma is {{ mode === 'quiz' ? 'reviewing your answer' : mode === 'practice' ? 'reviewing your practice' : 'preparing your explanation' }}…</div>
          <div v-if="tutorError" class="tutor-error" role="alert">
            <span>{{ tutorError }}</span>
            <button
              v-if="failedTutorPrompt"
              class="retry-btn"
              :disabled="tutorLoading"
              @click="askTutor(failedTutorPrompt, !practiceAnswer.trim(), failedTutorDisplayText)"
            >Try again</button>
          </div>
          <small v-if="tutorModel && !tutorLoading" class="model-label">Powered by {{ tutorModel }} through Hugging Face Inference Providers</small>

          <form v-if="mode !== 'quiz'" class="tutor-composer" @submit.prevent="askTutor()">
            <textarea
              v-model="tutorInput"
              rows="2"
              maxlength="2000"
              :placeholder="mode === 'quiz' ? 'Type your answer or ask Gemma a question…' : 'Ask a follow-up question…'"
              aria-label="Message Gemma"
              :disabled="tutorLoading"
              @keydown.enter.exact.prevent="askTutor()"
            ></textarea>
            <button class="primary-btn" type="submit" :disabled="tutorLoading || !tutorInput.trim()">
              {{ tutorLoading ? 'Thinking…' : 'Send →' }}
            </button>
          </form>
          <small class="privacy-note">Your message is sent to Hugging Face to generate a response. The Hugging Face token stays on the server.</small>
        </section>

        <footer class="lesson-footer">
          <span>Human-written lessons · AI-powered support from Gemma.</span>
          <span>{{ completedLessons.includes(topic) ? 'TOPIC COMPLETED ✓' : 'TAKE YOUR TIME' }}</span>
        </footer>
      </section>
    </main>
  </div>
</template>
