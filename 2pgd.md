# G17: ipexCommunicationService.recursiveSchemaResolve (src/core/agent/services/ipexCommunicationService.ts:1298-1339) treats every definition under schema.properties.e as a mandatory child schema, even when the credential has no e; SEDI SAM's optional utahAgent edge (no const s) and group edge guardians cannot resolve, so a SEDI credential cannot be admitted. Evidence: keri-conformance-suite interop/two-wallets INCOMPATIBILITIES.md #10
kind: debt
tags: interop, g17
created: 2026-10-12T22:01Z

