---
description: Random java tricks n tips
---

# Hacks

```bash
#get major version out of a classfile
javap -verbose ${classfile} | grep "major" | sed 's/^ *//g' | cut -d':' -f2;
```



