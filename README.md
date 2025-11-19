# Todo App (localStorage)

A small but fully functional Todo app that stores tasks in **localStorage**, lets you add, edit, and delete tasks, and protects you from accidentally discarding unsaved changes.

---

## Features

- **Add tasks**
  - Title (required)
  - Optional date
  - Optional description
- **Edit tasks**
  - Clicking **Edit** loads the task into the form
  - Button label switches from **Add Task** → **Update Task**
- **Delete tasks**
  - Removes the task from the UI and from localStorage
- **Local persistence**
  - All tasks are saved in `localStorage` under the `"data"` key
  - Tasks re-render automatically on page load
- **Unsaved changes protection**
  - If you’ve typed into the form and try to close it, a `<dialog>` pops up:
    - **Cancel** to go back
    - **Discard** to reset the form
- **Clean IDs and titles**
  - Titles and descriptions are sanitized to remove special characters
  - Each task gets a unique id that includes the sanitized title + timestamp

---

## Tech Stack

- **HTML** – structure, dialog, form, and task list container
- **CSS** – card layout, responsive sizing, buttons, modal styling
- **JavaScript** – state management, DOM manipulation, and localStorage

---

## How It Works (JavaScript Overview)

### State & Elements

```js
const taskData = JSON.parse(localStorage.getItem("data")) || [];
let currentTask = {};
taskData is the in-memory array backing the UI.

currentTask tracks the task being edited (if any).

Sanitizing Input
js
Copy code
const removeSpecialChars = (val) => {
  return val.trim().replace(/[^A-Za-z0-9\-\s]/g, '');
};
Used for:

title

description

id generation

Add / Update Task
js
Copy code
const addOrUpdateTask = () => {
  if (!titleInput.value.trim()) {
    alert("Please provide a title");
    return;
  }

  const dataArrIndex = taskData.findIndex(
    (item) => item.id === currentTask.id
  );

  const taskObj = {
    id: `${removeSpecialChars(titleInput.value).toLowerCase().split(" ").join("-")}-${Date.now()}`,
    title: removeSpecialChars(titleInput.value),
    date: dateInput.value,
    description: removeSpecialChars(descriptionInput.value),
  };

  if (dataArrIndex === -1) {
    taskData.unshift(taskObj);
  } else {
    taskData[dataArrIndex] = taskObj;
  }

  localStorage.setItem("data", JSON.stringify(taskData));
  updateTaskContainer();
  reset();
};
If currentTask matches an existing item → update

Otherwise → unshift to the top of the list

Rendering Tasks
js
Copy code
const updateTaskContainer = () => {
  tasksContainer.innerHTML = "";

  taskData.forEach(({ id, title, date, description }) => {
    tasksContainer.innerHTML += `
      <div class="task" id="${id}">
        <p><strong>Title:</strong> ${title}</p>
        <p><strong>Date:</strong> ${date}</p>
        <p><strong>Description:</strong> ${description}</p>
        <button onclick="editTask(this)" type="button" class="btn">Edit</button>
        <button onclick="deleteTask(this)" type="button" class="btn">Delete</button> 
      </div>
    `;
  });
};
Edit / Delete
Delete

js
Copy code
const deleteTask = (buttonEl) => {
  const dataArrIndex = taskData.findIndex(
    (item) => item.id === buttonEl.parentElement.id
  );

  buttonEl.parentElement.remove();
  taskData.splice(dataArrIndex, 1);
  localStorage.setItem("data", JSON.stringify(taskData));
};
Edit

js
Copy code
const editTask = (buttonEl) => {
  const dataArrIndex = taskData.findIndex(
    (item) => item.id === buttonEl.parentElement.id
  );

  currentTask = taskData[dataArrIndex];

  titleInput.value = currentTask.title;
  dateInput.value = currentTask.date;
  descriptionInput.value = currentTask.description;

  addOrUpdateTaskBtn.innerText = "Update Task";
  taskForm.classList.toggle("hidden");
};
Unsaved Changes Dialog
Compares current form values vs currentTask

If changed, opens the <dialog>; otherwise simply resets

js
Copy code
closeTaskFormBtn.addEventListener("click", () => {
  const formInputsContainValues =
    titleInput.value || dateInput.value || descriptionInput.value;
  const formInputValuesUpdated =
    titleInput.value !== currentTask.title ||
    dateInput.value !== currentTask.date ||
    descriptionInput.value !== currentTask.description;

  if (formInputsContainValues && formInputValuesUpdated) {
    confirmCloseDialog.showModal();
  } else {
    reset();
  }
});
Layout & Styling (CSS)
Dark page background: #0a0a23

White todo card with golden border

Responsive form and card size:

300x350 on small screens

400x450 on wider screens

Buttons use a yellow gradient with hover state

Form is absolutely positioned in the center of the todo card

Project Structure
text
Copy code
todo-app-localStorage/
├── index.html    # Layout, form, dialog, containers
├── styles.css    # Styling and responsive layout
└── script.js     # App logic, localStorage, events
What I Practiced
Using localStorage to persist data

Managing app state in plain JS

Generating unique IDs with Date.now()

Building modal flows with <dialog> plus Cancel/Discard buttons

Handling add/update logic with a single form

Future Improvements
Allow marking tasks as completed

Add filters (All / Active / Completed)

Add search or date filtering

Persist sort order or add drag-and-drop

Refactor to separate view vs data logic

Author
Created by Trevyn Sanders.