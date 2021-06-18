---
description: >-
  there is a way to write small python script inside a shell script, and call it
  like a function
---

# Python inside shell script

```bash
#shell function which returns a py string
get_qgid() {
  cat <<EOF
import json, sys
data = json.load(sys.stdin)['qualitygates']
qgName="${profile}"
for gates in data:
    if(qgName == gates['name']):
        print(gates['id'])
EOF
}
#passing output of curl command to py-shell-function
gateId=$(curl -s -X POST -u admin:admin ${SQROOT}/api/qualitygates/list | python -c "$( get_qgid "$1" )")
```

