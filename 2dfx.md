# Feature: PAP (Principal Action Protocol) support — escalate-to-human + policy auto-decide
kind: idea
created: 2026-07-15T18:43Z

- 2026-07-15T18:43Z PAP (Principal Action Protocol): a KERI exn-based protocol. A remote requester sends an action proposal carrying a hierarchical goal code to the principal/agent on /pap/ routes; the agent evaluates the goal against a YAML policy (allow/deny/ask; most-specific match wins; unmatched defaults to ask) and returns ack -> accepted/declined -> done/failed. allow = auto-execute, deny = auto-reject, ask = escalate to the human principal.

Feasibility: HIGH, low risk. The wallet already ingests KERI exn notifications in src/core/agent/services/keriaNotificationService.ts, dispatches NotificationAddedEvent, and enqueues approvals via stateCache.queueIncomingRequest rendered by the IncomingRequest page + SignRequest approval UI. A PAP ask maps directly onto that flow. Missing: a policy store, an evaluator service, and the escalation wiring.

TDD plan (strict red-green-refactor):
1. RED papPolicyRecord.test.ts: YAML parses to a PapPolicy record (goal patterns, rules, metadata). GREEN src/core/agent/records/papPolicyRecord.ts + papPolicyStorage.ts on BasicStorage; verify round-trip.
2. RED papPolicyService.test.ts: evaluate(goalCode, policy) returns allow|deny|ask with specificity (concrete > wildcard) and unmatched -> ask. GREEN src/core/agent/services/papPolicyService.ts.
3. RED papDecisionRecord.test.ts: persist + query a decision audit trail. GREEN papDecisionRecord.ts + papDecisionStorage.ts.
4. RED papEscalationHandler.test.ts: inbound /pap/ exn -> evaluate -> on ask emit PapEscalationEvent + enqueue an incoming request. GREEN handler wired into keriaNotificationService.ts route dispatch.
5. RED PapEscalation.test.tsx: render goal/requester/action; approve/deny dispatches the decision. GREEN src/ui/pages/IncomingRequest/PapEscalation.tsx reusing SignRequest logic; add IncomingRequestType.PAP_ESCALATION to stateCache.types.ts + an IncomingRequest.tsx branch.
6. RED policy-config settings test: load/save/validate YAML. GREEN a Settings/PapPolicy page -> papPolicyStorage; default unknown-goal behavior in the notificationsPreferences slice.

Effort ~3-5 days TDD. Analysis 2026-07-15.
