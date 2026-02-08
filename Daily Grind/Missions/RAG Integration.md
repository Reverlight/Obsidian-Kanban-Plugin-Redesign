
## Not Done

- [ ] Vector Database Setup 📅 2026-02-08
  status:: not-done
  xp:: 30
  coins:: 20

- [ ] Loading & Chunking PDFs 📅 2026-02-05
  status:: not-done
  xp:: 30
  coins:: 20

- [ ] Querying out VectorDB 📅 2026-02-05
  status:: not-done
  xp:: 30
  coins:: 20

- [ ] Adding the Frontend 📅 2026-02-05
  status:: not-done
  xp:: 30
  coins:: 20

- [ ] Rate Limiting, Throttling & Concurrency 📅 2026-02-05
  status:: not-done
  xp:: 30
  coins:: 20

- [ ] Obsidian make notes (Life Game Mode) 📅 2026-02-05
  status:: not-done
  xp:: 30
  coins:: 20

- [ ] Obsidian notes 📅 2026-02-05
  status:: not-done
  xp:: 30
  coins:: 20

- [ ] New Mission 📅 2026-02-05
	status:: not-done
	xp:: 30
	coins:: 20


## Done

- [x] Ingest Dev Server Setup 📅 2026-02-08 ✅ 2026-02-08
  status:: done
  xp:: 30
  coins:: 20

- [x] API Setup 📅 2026-02-05
  status:: done
  xp:: 30
  coins:: 20

- [x] Project Setup & Dependencies 📅 2026-02-05
  status:: done
  xp:: 30
  coins:: 20

- [x] Architecture & Tools Breakdown 📅 2026-02-05
  status:: done
  xp:: 30
  coins:: 20

- [x] Project Demo 📅 2026-02-05
  status:: done
  xp:: 30
  coins:: 20

- [x] Overview 📅 2026-02-05
  status:: done
  xp:: 30
  coins:: 20


## Queried tasks
```dataviewjs

dv.taskList(
  dv.current()
    .file.tasks
    .mutate(t => t.visual = t.text.split("").join("") ),
  false)
```


```dataviewjs
const tasks = dv.current().file.tasks;
const total = tasks.length;
const completed = tasks.filter(t => t.completed).length;
const pct = total > 0 ? Math.round((completed / total) * 100) : 0;

// Create styled progress bar
const progressHTML = `
<div style="margin-bottom: 10px;">
  <div style="background: #ddd; border-radius: 10px; overflow: hidden; height: 20px;">
    <div style="background: linear-gradient(90deg, #4caf50, #8bc34a); width: ${pct}%; height: 100%; transition: width 0.3s;"></div>
  </div>
  <div style="margin-top: 5px; text-align: center; font-weight: bold;">
    ${pct}% Complete (${completed}/${total} tasks)
  </div>
</div>
`;

dv.paragraph(progressHTML);

dv.taskList(
  tasks.mutate(t => t.visual = t.text.split("").join("")),
  false
);
```