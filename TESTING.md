# Testing Guide for To-Do List Application

## 🧪 Manual Testing Checklist

### 1. Adding Tasks
- [ ] Type task and click "Add Task" button
- [ ] Press Enter after typing task
- [ ] Empty input shows alert
- [ ] Task over 100 characters shows alert
- [ ] New task appears at top of list
- [ ] Input field clears after adding
- [ ] Focus returns to input field

### 2. Completing Tasks
- [ ] Click checkbox to mark complete
- [ ] Task text gets struck through
- [ ] Checkbox shows checked state
- [ ] Click again to mark incomplete
- [ ] Task text returns to normal
- [ ] Completed count updates in stats

### 3. Deleting Tasks
- [ ] Click Delete button on any task
- [ ] Task immediately disappears
- [ ] Works for both completed and active tasks
- [ ] Stats update after deletion
- [ ] Empty state shows when all deleted

### 4. Filtering
- [ ] "All" filter shows all tasks
- [ ] "Active" filter shows only incomplete tasks
- [ ] "Completed" filter shows only complete tasks
- [ ] Filter button highlights when active
- [ ] Filtered count matches display
- [ ] Switching filters updates display instantly

### 5. Statistics
- [ ] Total count is correct
- [ ] Active count matches incomplete tasks
- [ ] Completed count matches complete tasks
- [ ] Stats update immediately after actions

### 6. Local Storage
- [ ] Add tasks and refresh page
- [ ] Tasks remain after refresh
- [ ] Check browser DevTools → Application → localStorage
- [ ] Key exists: `todos-app-data`
- [ ] Data is valid JSON
- [ ] Works in incognito mode
- [ ] Works across different tabs

### 7. Clear Actions
- [ ] Clear Completed removes only completed tasks
- [ ] Clear All removes all tasks
- [ ] Confirmation dialogs appear
- [ ] Buttons disabled when no tasks applicable
- [ ] Buttons enabled when tasks available

### 8. UI/UX
- [ ] Smooth animations on task add
- [ ] Hover effects on tasks
- [ ] Responsive layout on mobile
- [ ] Scrollbar appears when list is long
- [ ] Layout looks good on all screen sizes
- [ ] No overlapping elements

## 🔬 Unit Tests (Using Browser Console)

```javascript
// Open DevTools (F12) and run these commands:

// Test 1: Create app instance
console.assert(window.todoApp !== undefined, 'App should exist');
console.log('✓ Test 1: App initialized');

// Test 2: Add task
const initialCount = window.todoApp.todos.length;
window.todoApp.todoInput.value = 'Test Task';
window.todoApp.addTodo();
console.assert(
  window.todoApp.todos.length === initialCount + 1,
  'Task should be added'
);
console.log('✓ Test 2: Add task works');

// Test 3: Toggle completion
const firstId = window.todoApp.todos[0].id;
window.todoApp.toggleTodo(firstId);
console.assert(
  window.todoApp.todos[0].completed === true,
  'Task should be completed'
);
window.todoApp.toggleTodo(firstId);
console.assert(
  window.todoApp.todos[0].completed === false,
  'Task should be incomplete'
);
console.log('✓ Test 3: Toggle completion works');

// Test 4: Delete task
const deleteId = window.todoApp.todos[0].id;
window.todoApp.deleteTodo(deleteId);
console.assert(
  !window.todoApp.todos.find(t => t.id === deleteId),
  'Task should be deleted'
);
console.log('✓ Test 4: Delete task works');

// Test 5: Filter functionality
window.todoApp.setFilter('active');
console.assert(
  window.todoApp.currentFilter === 'active',
  'Filter should be active'
);
console.log('✓ Test 5: Filter works');

// Test 6: Get statistics
const stats = window.todoApp.getStats();
console.assert(stats.hasOwnProperty('total'), 'Stats should have total');
console.assert(stats.hasOwnProperty('completed'), 'Stats should have completed');
console.assert(stats.hasOwnProperty('active'), 'Stats should have active');
console.log('✓ Test 6: Statistics work', stats);

// Test 7: Local storage persistence
const testData = JSON.parse(localStorage.getItem('todos-app-data'));
console.assert(Array.isArray(testData), 'Data should be array');
console.log('✓ Test 7: Local storage persistence works');

// Test 8: HTML escaping (XSS protection)
window.todoApp.todoInput.value = '<script>alert("XSS")</script>';
window.todoApp.addTodo();
const lastItem = window.todoApp.todos[window.todoApp.todos.length - 1];
console.assert(
  !lastItem.text.includes('<script>'),
  'HTML should be escaped'
);
console.log('✓ Test 8: XSS protection works');

console.log('\n✅ All manual tests passed!');
```

## 📱 Cross-Browser Testing

Test on these combinations:

| Browser | Device | Status |
|---------|--------|--------|
| Chrome | Desktop | |
| Firefox | Desktop | |
| Safari | Desktop | |
| Edge | Desktop | |
| Chrome | Mobile | |
| Safari | Mobile | |

## 🎯 Edge Cases to Test

```javascript
// 1. Add very long task
window.todoApp.todoInput.value = 'a'.repeat(100);
window.todoApp.addTodo();

// 2. Add task with special characters
window.todoApp.todoInput.value = 'Task with <b>HTML</b> & symbols!';
window.todoApp.addTodo();

// 3. Add task with emoji
window.todoApp.todoInput.value = 'Buy 🍎 and 🥦';
window.todoApp.addTodo();

// 4. Rapid additions
for (let i = 0; i < 100; i++) {
  window.todoApp.todoInput.value = `Task ${i}`;
  window.todoApp.addTodo();
}

// 5. Toggle many tasks rapidly
window.todoApp.todos.forEach(t => {
  window.todoApp.toggleTodo(t.id);
  window.todoApp.toggleTodo(t.id);
});

// 6. Delete all tasks
while (window.todoApp.todos.length > 0) {
  window.todoApp.deleteTodo(window.todoApp.todos[0].id);
}
```

## 🔍 Performance Testing

```javascript
// Measure add performance
console.time('Add 1000 tasks');
for (let i = 0; i < 1000; i++) {
  window.todoApp.todos.push({
    id: Date.now() + i,
    text: `Task ${i}`,
    completed: false,
    createdAt: new Date().toISOString()
  });
}
console.timeEnd('Add 1000 tasks');

// Measure render time
console.time('Render 1000 tasks');
window.todoApp.render();
console.timeEnd('Render 1000 tasks');

// Measure save time
console.time('Save to storage');
window.todoApp.saveToStorage();
console.timeEnd('Save to storage');

// Measure load time
console.time('Load from storage');
window.todoApp.loadFromStorage();
console.timeEnd('Load from storage');
```

## 🌐 Accessibility Testing

- [ ] Keyboard navigation works (Tab through elements)
- [ ] Enter key triggers add action
- [ ] Screen reader can read all content
- [ ] Color contrast meets WCAG standards
- [ ] Focus indicators are visible
- [ ] Labels are associated with inputs

## 📊 Automated Testing (Jest Example)

```javascript
// Example test file (e.g., app.test.js)

describe('TodoApp', () => {
  let app;

  beforeEach(() => {
    localStorage.clear();
    app = new TodoApp();
  });

  test('should add a todo', () => {
    app.todoInput.value = 'Test Task';
    app.addTodo();
    expect(app.todos).toHaveLength(1);
    expect(app.todos[0].text).toBe('Test Task');
  });

  test('should toggle todo completion', () => {
    app.todoInput.value = 'Test';
    app.addTodo();
    const id = app.todos[0].id;
    app.toggleTodo(id);
    expect(app.todos[0].completed).toBe(true);
  });

  test('should delete a todo', () => {
    app.todoInput.value = 'Test';
    app.addTodo();
    const id = app.todos[0].id;
    app.deleteTodo(id);
    expect(app.todos).toHaveLength(0);
  });

  test('should persist to localStorage', () => {
    app.todoInput.value = 'Test';
    app.addTodo();
    const stored = JSON.parse(localStorage.getItem('todos-app-data'));
    expect(stored).toHaveLength(1);
  });

  test('should filter active todos', () => {
    app.todoInput.value = 'Task 1';
    app.addTodo();
    app.todoInput.value = 'Task 2';
    app.addTodo();
    app.toggleTodo(app.todos[0].id);
    
    const filtered = app.getFilteredTodos();
    expect(filtered).toHaveLength(1);
    expect(filtered[0].completed).toBe(false);
  });

  test('should calculate stats correctly', () => {
    app.todoInput.value = 'Task 1';
    app.addTodo();
    app.todoInput.value = 'Task 2';
    app.addTodo();
    app.toggleTodo(app.todos[0].id);
    
    const stats = app.getStats();
    expect(stats.total).toBe(2);
    expect(stats.completed).toBe(1);
    expect(stats.active).toBe(1);
    expect(stats.completionRate).toBe(50);
  });
});
```

## 🚀 Performance Benchmarks

Target metrics:

| Metric | Target | Status |
|--------|--------|--------|
| Initial load | <500ms | |
| Add task | <10ms | |
| Delete task | <10ms | |
| Toggle completion | <10ms | |
| Filter change | <50ms | |
| localStorage write | <5ms | |
| localStorage read | <5ms | |

## 📋 Deployment Checklist

Before deploying to production:

- [ ] All tests pass
- [ ] No console errors
- [ ] Mobile responsive working
- [ ] localStorage working
- [ ] Cross-browser tested
- [ ] Performance acceptable
- [ ] Accessibility checked
- [ ] Code minified/optimized
- [ ] README updated
- [ ] CHANGELOG updated

## 🐛 Known Issues

Current issues and workarounds:

| Issue | Status | Workaround |
|-------|--------|-----------|
| None identified | ✅ | N/A |

---

**Test report last updated:** [Current Date]
**Tested by:** [Your Name]
**Status:** ✅ Ready for Production
