# 🎮 Character: Dashrs

> [!info] Character Stats **Level:** 8 **Power Rank:** Apprentice **Total XP:** 8250 XP **Total Coins:** 6336 Coins **Progress to Next Level:** 25% ████░░░░░░░░░░░░

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

// Add your image here - replace with your image path
dv.paragraph(`![Profile Image](head-empty-fox-girl-ezgif.com-crop.webp)`);

dv.paragraph(`
**Title:** ${title}  
**Level:** ${level}  
**Completed Quests:** ${questCount}  
**Quests to Next Level:** ${questsToNextLevel}
`);

// Progress bar
const filledBlocks = Math.floor(progressPercent / 10);
const emptyBlocks = 10 - filledBlocks;
const progressBar = "▓".repeat(filledBlocks) + "░".repeat(emptyBlocks);

dv.paragraph(`**Progress:** [${progressBar}] ${progressPercent.toFixed(0)}%`);

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
  title as "Title"
FROM "Daily Grind/Quests"
WHERE completed = True
```

### ✅ Done

```dataview
TABLE WITHOUT ID
  file.link as "Quest",
  completed as "Completed",
  points as "XP Earned"
FROM "Daily Grind/Quests"
WHERE status = "done"
SORT completed DESC
LIMIT 10
```

```dataview
TABLE WITHOUT ID sum(rows.file.tasks.completed) as "✅ Total Completed Today", sum(rows.file.tasks) as "📋 Total Tasks Today", (sum(rows.file.tasks.completed) * 100) as "💎 Total XP Earned" FROM "Daily Grind/Quests" WHERE type = "quest" and date = date(today)
```

---

## 📅 This Week's Grind

### Good Habits Completed

```dataview
LIST
```

### Weekly Stats

```dataview
TABLE WITHOUT ID
  sum(rows.xp) as "Total XP This Week",
  sum(rows.coins) as "Total Coins This Week",
  length(rows) as "Habits Completed"
FROM "Daily Grind/Good Habits"
WHERE dateCompleted >= date(today) - dur(7 days)
```

---

## 🎯 Quick Actions

- [[Daily Note Template|Create Today's Quest]]
- [[Weekly Review|Weekly Review]]
- [[Reflection Template|New Reflection]]

---

### Habit Streaks

```dataview
TABLE WITHOUT ID
  file.link as "Habit",
  streak as "Current Streak"
FROM "Daily Grind/Good Habits"
WHERE streak > 0
SORT streak DESC
```