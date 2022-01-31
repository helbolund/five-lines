# Mortens noter til "5 lines of code"

## Rules
### Five Lines
### Either Call or Pass
Either ***call*** methods on an object or ***pass*** the object, not both
### If Only at the Start
### Function naming
- Honest - Describe the functions intention
- Complete - Capture all the functions does
- Understandable - Be undestandable for someone working in the domain

### Never use if with else
Unless we are checking against a data type we do not control.
If elses are hardcoded decisions
Map third party types into types we control
Stand alone ***if***s are considered checks and ok at the start, ***if-else*** are decisions

Aim for late binding rather than early binding as else-if dictates. Also change by addition
