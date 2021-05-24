---
description: Random java tricks n tips
---

# Tips and tricks

```bash
#get major version out of a classfile
javap -verbose ${classfile} | grep "major" | sed 's/^ *//g' | cut -d':' -f2;
```



