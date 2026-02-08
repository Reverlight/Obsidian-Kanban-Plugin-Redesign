# 🎮 Character: Dashrs

```dataviewjs
// Get all completed quests
const completedQuests = dv.pages('"Daily Grind/Quests"')
    .where(p => p.completed === true);
const questCount = completedQuests.length;
// Level calculation (50 quests per level)
const level = Math.floor(questCount / 50) + 1;
const questsToNextLevel = (level * 50) - questCount;
const progressPercent = ((questCount % 50) / 50) * 100;
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
**Quests to Next Level:** ${questsToNextLevel}  
**Progress to Next Level:** ${progressPercent.toFixed(0)}%
<div style="background-color: #e0e0e0; border-radius: 10px; height: 20px; width: 100%; overflow: hidden; margin-top: 8px;">
  <div style="background-color: #3b82f6; height: 100%; width: ${progressPercent}%; transition: width 0.3s ease;"></div>
</div>
`);
// Optional: Show recent completed quests
dv.header(3, "Recently Completed Quests");
dv.table(
    ["Quest", "Title"],
    completedQuests
        .sort(p => p.file.mtime, 'desc')
        .limit(5)
        .map(p => [p.file.link, p.title || ""])
);
```


---
## 📊 Daily Grind Overview

### 🎯 Not Started


```dataview
TABLE WITHOUT ID
  file.link as "Quest",
  title as "Title",
  points as "XP"
FROM "Daily Grind/Quests"
WHERE !completed AND status != "done"
SORT file.name ASC
```

### ✅ Completed

```dataview
TABLE WITHOUT ID
  file.link as "Quest",
  title as "Title",
  completed as "Completed Date"
FROM "Daily Grind/Quests"
WHERE completed = true OR status = "done"
SORT completed DESC
LIMIT 10
```

### 📈 Today's Stats


```dataviewjs
const today = dv.date("today");

// Get today's completed quests
const todayQuests = dv.pages('"Daily Grind/Quests"')
  .where(p => {
    // Check if quest is completed AND dated today
    if (p.completed === true && p.date) {
      return dv.date(p.date).equals(today);
    }
    return false;
  });

// Get today's incomplete quests
const todayQuestsNotCompleted = dv.pages('"Daily Grind/Quests"')
  .where(p => {
    // Check if quest is NOT completed AND dated today
    if (p.completed === false && p.date) {
      return dv.date(p.date).equals(today);
    }
    return false;
  });

const completedCount = todayQuests.length;
const notCompletedCount = todayQuestsNotCompleted.length;
const totalTodayQuests = completedCount + notCompletedCount;

// All-time stats
const allQuests = dv.pages('"Daily Grind/Quests"').length;
const completedAll = dv.pages('"Daily Grind/Quests"').where(p => p.completed === true).length;
const remainingQuests = allQuests - completedAll;

// Calculate level based on total completed quests (50 per level)
const level = Math.floor(completedAll / 50) + 1;
const questsToNextLevel = (level * 50) - completedAll;

// XP calculations (100 XP per quest)
const xpEarnedToday = completedCount * 100;
const totalXP = completedAll * 100;
const xpToNextLevel = questsToNextLevel * 100;

dv.paragraph(`
> [!success] Daily Progress
> **📅 Today's Quests:** ${completedCount}/${totalTodayQuests} completed
> **💎 XP Earned Today:** ${xpEarnedToday}
> **🎯 Remaining Today:** ${notCompletedCount}
> 
> **⚔️ Current Level:** ${level}
> **📊 Total XP:** ${totalXP.toLocaleString()}
> **🔼 Quests to Next Level:** ${questsToNextLevel} (${xpToNextLevel.toLocaleString()} XP)
> **📋 All-Time Remaining:** ${remainingQuests}
`);
```
