# Drizzle ORM

## Relational queries

- [Include relations](https://orm.drizzle.team/docs/rqb#include-relations)
- [Partial fields select](https://orm.drizzle.team/docs/rqb#partial-fields-select)
- [Select filters](https://orm.drizzle.team/docs/rqb#select-filters)
- [Limit & Offset](https://orm.drizzle.team/docs/rqb#limit--offset)
- [Order By](https://orm.drizzle.team/docs/rqb#order-by)

```typescript
import { db } from '$lib/server/db/index.js';
import { programs, reservations } from '$lib/server/db/schema.js';
import { and, desc, gte, isNotNull, lt } from 'drizzle-orm';

// $lib/server is an alias used in SvelteKit projects.

await db.query.programs.findMany({
  with: {
    events: true,
    reservations: {
      where: isNotNull(reservations.canceledAt),
    },
  },
  columns: { name: false },
  where: and(gte(programs.minGrade, 1), lt(programs.maxGrade, 4)),
  limit: 5, // from the 5th to the 10th
  offset: 5, // offset is only available for top level query
  orderBy: [desc(programs.id)],
});
```
