# Web 500s over threshold
This alert fires when there are too many 500s.
 
## Investigate

Check ELB Logs or the ECS metrics

[Run workflow](https://console.demo.transposit.com/mc/t/transposit/actions/action1)


## Remediate
Scale ECS

[Scale ECS](https://console.demo.transposit.com/mc/t/transposit/actions/scale_ecs)

[Remediation runbook](https://console.demo.transposit.com/mc/t/transposit/runbooks/remediation_runbook)

## Verify OK
Check that 500s are back to a normal level

[Cloudwatch logs](https://console.demo.transposit.com/mc/t/transposit/actions/cloudwatch_logs)