---
description: >-
  there is a way to write small python script inside a shell script, and call it
  like a function
---

# Python inside shell script

```python
#!/bin/ksh

#shell function which returns a py file
get_qgid() {
  cat <<EOF
import json, sys
#sys.stdin provides the cmd input
data = json.load(sys.stdin)['qualitygates']
qgName="sonar_way"
for gates in data:
    if(qgName == gates['name']):
        print(gates['id'])
EOF
}
#piping the curl command output to py-shell-function
gateId=$(curl -s -X POST -u admin:admin ${SQROOT}/api/qualitygates/list | python -c "$( get_qgid "$1" )")
```

