# Exam Quiz GitHub Pages Site

This is a static GitHub Pages quiz site. It reads one Firestore document in the browser and lets public users answer MCQs, then see the selected answer, correction, and explanation.

The quiz presents one MCQ per screen for mobile use. Each chapter attempt randomly selects up to 30 questions. Selecting an option immediately stops speech and shows Hindi feedback (correct or incorrect), the correct answer, and the explanation stored with that question. Previous/Next controls preserve selected answers, and supported browsers read the current question and options aloud when the question opens.

Use **My progress** to see one card per subject with colored chapter-level learning completion, the latest attempt date, completed count out of 30, and subject progress calculated across the 11 chapters. Repeated attempts on the same question do not inflate completion. Attempts are stored in the browser's local storage on the current device; no sign-in or cloud user profile is configured yet.

## Firestore document

The current quiz document is:

`quizzes/academics_english_cbse_10th_english_low`

The page currently reads this document ID from `index.html`.

## Firebase web configuration

1. In Firebase Console, open **Project settings > Your apps**.
2. Register a Web App if one does not exist.
3. Copy its Firebase configuration object into `index.html`, replacing the three `REPLACE_WITH_...` values.
4. In **Firestore Database > Rules**, allow read access only for the public study material that this page needs. For example:

    ```text
    rules_version = '2';
    service cloud.firestore {
       match /databases/{database}/documents {
          match /study_materials/1-T-vatwOiDDt9gHz1O1vXHRvILrLxsO476mFDXMgsn4 {
             allow read: if true;
             allow write: if false;
          }
          match /{document=**} {
             allow read, write: if false;
          }
       }
    }
   ```

Firebase Web API keys are designed to be included in browser code. Firestore Rules are what protect the database. Never put a Firebase Admin service-account JSON file in this repository.

## Publish on GitHub Pages

1. Create a GitHub repository and upload `index.html`.
2. Open the repository's **Settings > Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, select the branch containing `index.html` and the `/ (root)` folder, then save.
4. GitHub will provide a public URL such as `https://YOUR-USERNAME.github.io/YOUR-REPOSITORY/`.

The site may take a minute or two to become available after the first deployment.

## MCQ data shape

The renderer accepts a document containing `questions`, `mcqs`, `items`, or `quiz` as an array. Each question can use these names:

- Question text: `question`, `questionText`, `text`, or `prompt`
- Choices: `options`, `choices`, or `answers`
- Correction: `correctAnswer`, `correct_answer`, `answer`, `correct`, or `correction`
- Explanation: `explanation`, `solution`, or `reason`

The correction may be the exact option text or its zero-based array index.

## Chapter documents on GitHub

Chapter study documents are hosted as public PDF files in this repository. The chapter-selection page generates one document link for every chapter using this path pattern:

```text
documents/{medium}/{subject-slug}/chapter-{number}.pdf
```

The application opens these files through the GitHub Pages site using a relative URL. This shows only the PDF in the browser, without the GitHub repository portal. For example:

```text
https://examquiz.github.io/Bridge-Course/documents/hindi/childdev_educational_psychology/chapter-01.pdf
```

For example:

```text
documents/hindi/curriculum_pedagogy_assessment/chapter-01.pdf
documents/english/curriculum_pedagogy_assessment/chapter-01.pdf
documents/hindi/childdev_educational_psychology/chapter-01.pdf
```

Create the folders and add PDFs using the exact medium names, document slugs, and two-digit chapter numbers used by the application. The first subject uses `childdev_educational_psychology` for documents even though its Firestore quiz slug is `childdev&educational_psychology`. The link appears as **Read document** beside the MCQ action for each chapter. The PDF opens in a new browser tab; it does not start the quiz.

Recommended repository layout:

```text
documents/
   hindi/
      curriculum_pedagogy_assessment/
         chapter-01.pdf
         chapter-02.pdf
   english/
      curriculum_pedagogy_assessment/
         chapter-01.pdf
```

Commit and push the PDF files to the `main` branch of `ExamQuiz/Bridge-Course`, then wait for GitHub Pages to deploy the `documents` folder. In GitHub, open **Settings > Pages** and confirm the source is the `main` branch and `/ (root)` folder. If the direct Pages URL returns 404, the PDF has not been included in the current Pages deployment yet; do not replace it with the `blob/main` viewer URL because that exposes the GitHub portal.

Keep documents reasonably small. GitHub blocks files larger than 100 MB, and large binary collections increase repository size and deployment time. These documents are public when the repository or Pages site is public, so do not commit private or sensitive material.

## Chapter names

The header **EXAM TIMELINE** link expects this public PDF path:

```text
documents/examtimeline/bridge-academic-calendar.pdf
```

Upload the file with the exact lowercase name `bridge-academic-calendar.pdf` and commit it to the same branch and root published by GitHub Pages.

Edit the `chapterNames` array for each subject in `index.html` to replace the default placeholders:

```javascript
chapterNames: [
   "Understanding Childhood",
   "Developmental Stages",
   "Learning and Maturation"
]
```

The first array item is shown for Chapter 1, the second for Chapter 2, and so on. Hindi uses `chapterNames`; English uses the separate `englishChapterNames` array. Keep 11 names in each array. Until an English name is added, the English placeholder remains visible. If any entry is missing, the application falls back to `Chapter N name`, and the quiz and document links continue to work.

Example:

```javascript
{
   name: "Curriculum, Pedagogy & Assessment",
   chapterNames: ["पाठ्यचर्या", "..."],
   englishChapterNames: ["Curriculum", "Chapter 2 name"]
}
```
