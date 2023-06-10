---
aliases: []
tags: weekly
---

```dataviewjs
const format =  'gggg-[W]ww';
dv.paragraph(`<<[[${moment(dv.current().file.name, format).subtract(1, "week").format(format)}|上周周记]]  [[${moment(dv.current().file.name, format).add(1,  "week").format(format)}||下周周记]]>>`)
```
