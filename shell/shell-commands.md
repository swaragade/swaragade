---
description: Random shell commands library
---

# Shell commands

```bash
#rename with date as extention
mv ${CompareLogs} ${CompareLogs}.$(date -u '+%y%m%d-%H:%M') 
```

```bash
# print on console as well as log file
printSuccess()
{
	echo "$1" | tee -a ${SuccessReportFile}
}
```

```bash
# Read and check user arguments
while getopts ":b:t:f:m:e" option
do
   case ${option} in
		b )  BRANCH=${OPTARG} ;;
		t )  TYPE_INPUT=${OPTARG} ;;
		f )  FILE_INPUT=${OPTARG} ;;
		m )  MODULE_INPUT=${OPTARG} ;;
		\? ) echo "Invalid option: -${OPTARG}"; callThisFunction; return 1 ;;
		: ) echo "Option -${OPTARG} requires an argument."; callThisFunction; return 1 ;;
   esac
done
```



