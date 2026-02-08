# 🎮 Character: Dashrs

```dataviewjs
// Get all completed quests
const completedQuests = dv.pages('"Daily Grind/Quests"')
    .where(p => p.completed === true);

// Get all completed missions from the Missions folder
const completedMissions = dv.pages('"Daily Grind/Missions"')
    .where(p => {
        const tasks = p.file.tasks;
        return tasks && tasks.filter(t => t.completed).length > 0;
    })
    .map(p => {
        const tasks = p.file.tasks;
        return {
            page: p,
            completedCount: tasks.filter(t => t.completed).length
        };
    })
    .array(); // Convert to array here

// Calculate total completed count
const questCount = completedQuests.length;
const missionTaskCount = completedMissions.reduce((sum, m) => sum + m.completedCount, 0);
const totalCompleted = questCount + missionTaskCount;

// Level calculation (50 completions per level)
const level = Math.floor(totalCompleted / 50) + 1;
const completionsToNextLevel = (level * 50) - totalCompleted;
const progressPercent = ((totalCompleted % 50) / 50) * 100;

// Title system based on level
function getTitle(lvl) {
    if (lvl >= 50) return "Legendary Hero";
    if (lvl >= 40) return "Master Questor";
    if (lvl >= 30) return "Epic Adventurer";
    if (lvl >= 25) return "Veteran Explorer";
    if (lvl >= 20) return "Seasoned Warrior";
    if (lvl >= 15) return "Skilled Adventurer";
    if (lvl >= 10) return "Experienced Quester";
    if (lvl >= 7) return "Competent Explorer";
    if (lvl >= 5) return "Apprentice Adventurer";
    if (lvl >= 3) return "Novice Quester";
    return "Aspiring Hero";
}

const title = getTitle(level);

// Render the display
dv.header(2, "⚔️ Quest Progress");

// Add your image here
dv.paragraph(`![Profile Image](head-empty-fox-girl-ezgif.com-crop.webp)`);

// Info box with character stats and blue progress bar
dv.paragraph(`
**Title:** ${title}  
**Level:** ${level}  
**Completed Quests:** ${questCount}  
**Completed Mission Tasks:** ${missionTaskCount}  
**Total Completed:** ${totalCompleted}  
**To Next Level:** ${completionsToNextLevel}  
**Progress to Next Level:** ${progressPercent.toFixed(0)}%

<div style="background-color: #e0e0e0; border-radius: 10px; height: 20px; width: 100%; overflow: hidden; margin-top: 8px;">
  <div style="background-color: #3b82f6; height: 100%; width: ${progressPercent}%; transition: width 0.3s ease;"></div>
</div>
`);

// Show recent completed quests
dv.header(3, "Recently Completed Quests");
dv.table(
    ["Quest", "Title"],
    completedQuests
        .sort(p => p.file.mtime, 'desc')
        .limit(5)
        .map(p => [p.file.link, p.title || ""])
);

// Show missions with completed tasks
dv.header(3, "Mission Progress");
dv.table(
    ["Mission", "Completed Tasks"],
    completedMissions
        .sort((a, b) => b.page.file.mtime - a.page.file.mtime)
        .map(m => [m.page.file.link, m.completedCount])
);
```


---
## 📊 Daily Grind Overview

### 🎯 Not Started


```dataview
TABLE WITHOUT ID file.link as "Quest", title as "Title", points as "XP" FROM "Daily Grind/Quests" WHERE !completed AND status != "done" AND date = date(today) SORT file.name ASC
```

### ✅ Completed

```dataview
TABLE WITHOUT ID file.link as "Quest", title as "Title", completed as "Completed Date" FROM "Daily Grind/Quests" WHERE (completed = true OR status = "done") AND date = date(today) SORT completed DESC
```

### 📈 Today's Stats


```dataviewjs
const today = dv.date("today");

// Get today's completed quests
const todayQuests = dv.pages('"Daily Grind/Quests"')
  .where(p => {
    if (p.completed === true && p.date) {
      return dv.date(p.date).equals(today);
    }
    return false;
  });

// Get today's incomplete quests
const todayQuestsNotCompleted = dv.pages('"Daily Grind/Quests"')
  .where(p => {
    if (p.completed === false && p.date) {
      return dv.date(p.date).equals(today);
    }
    return false;
  });

// Get all missions and filter tasks by today's date
const allMissions = dv.pages('"Daily Grind/Missions"').array();

let todayMissionTasksCompleted = 0;
let todayMissionTasksTotal = 0;

allMissions.forEach(mission => {
  const tasks = mission.file.tasks;
  if (tasks) {
    tasks.forEach(task => {
      // Check if task has a date that matches today
      if (task.due && dv.date(task.due).equals(today)) {
        todayMissionTasksTotal++;
        if (task.completed) {
          todayMissionTasksCompleted++;
        }
      }
    });
  }
});

const todayMissionTasksRemaining = todayMissionTasksTotal - todayMissionTasksCompleted;

// Today's totals
const completedQuestsToday = todayQuests.length;
const notCompletedQuestsToday = todayQuestsNotCompleted.length;
const totalTodayQuests = completedQuestsToday + notCompletedQuestsToday;
const totalCompletedToday = completedQuestsToday + todayMissionTasksCompleted;
const totalTasksToday = totalTodayQuests + todayMissionTasksTotal;

// All-time stats
const allQuests = dv.pages('"Daily Grind/Quests"').length;
const completedAllQuests = dv.pages('"Daily Grind/Quests"').where(p => p.completed === true).length;
const remainingQuests = allQuests - completedAllQuests;

// All-time mission tasks
let completedAllMissionTasks = 0;
let totalAllMissionTasks = 0;

allMissions.forEach(mission => {
  const tasks = mission.file.tasks;
  if (tasks) {
    tasks.forEach(task => {
      totalAllMissionTasks++;
      if (task.completed) {
        completedAllMissionTasks++;
      }
    });
  }
});

const remainingMissionTasks = totalAllMissionTasks - completedAllMissionTasks;

// Combined all-time stats
const totalCompletedAll = completedAllQuests + completedAllMissionTasks;
const totalRemainingAll = remainingQuests + remainingMissionTasks;

// Calculate level based on total completed (50 per level)
const level = Math.floor(totalCompletedAll / 50) + 1;
const completionsToNextLevel = (level * 50) - totalCompletedAll;

// XP calculations (100 XP per completion)
const xpEarnedToday = totalCompletedToday * 100;
const totalXP = totalCompletedAll * 100;
const xpToNextLevel = completionsToNextLevel * 100;

dv.paragraph(`
> [!success] Daily Progress
> **📅 Today's Tasks:** ${totalCompletedToday}/${totalTasksToday} completed
> ├─ Quests: ${completedQuestsToday}/${totalTodayQuests}
> └─ Mission Tasks: ${todayMissionTasksCompleted}/${todayMissionTasksTotal}
> **💎 XP Earned Today:** ${xpEarnedToday}
> **🎯 Remaining Today:** ${totalTasksToday - totalCompletedToday}
> 
> **⚔️ Current Level:** ${level}
> **📊 Total XP:** ${totalXP.toLocaleString()}
> **🔼 To Next Level:** ${completionsToNextLevel} completions (${xpToNextLevel.toLocaleString()} XP)
> **📋 All-Time Stats:**
> ├─ Completed: ${totalCompletedAll.toLocaleString()} (${completedAllQuests} quests + ${completedAllMissionTasks} tasks)
> └─ Remaining: ${totalRemainingAll.toLocaleString()} (${remainingQuests} quests + ${remainingMissionTasks} tasks)
`);
```
