---
aliases: []
tags: daily
---

```dataviewjs
const format =  'YYYY-MM-DD';
dv.paragraph(`<<[[${moment(dv.current().file.name, format).subtract(1, "day").format(format)}|昨天日记]]  [[${moment(dv.current().file.name, format).add(1,  "day").format(format)}||明天日记]]>>`)
```
