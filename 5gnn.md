# Feature: human-to-human verification (in-wallet proof proposals via QR/PAP, in-wallet verify, received-proof archive w/ retention)
kind: idea
created: 2026-07-15T18:44Z

- 2026-07-15T18:45Z Extend the wallet so a human can challenge another human to prove something: compose canned or custom proof proposals in the wallet, present them to a remote user over the network (via PAP, see tick 2dfx) or via QR, verify the returned proof IN the wallet, and archive received proofs with a retention / terms-and-conditions lifecycle.

Maturity: PARTIAL. src/core/agent/services/ipexCommunicationService.ts handles holder-side flows (admitAcdcFromGrant, offerAcdcFromApply, grantAcdcFromAgree) but has NO verifier role: no in-wallet presentation validation. QR infra exists (@capacitor-mlkit/barcode-scanning in src/ui/components/Scan/Scan.tsx) but only for OOBI/connection setup. Connections model + BasicStorage/record patterns exist, but there is NO received-proof archive and NO retention/expiry/T&C lifecycle. Highest uncertainty: verifier-side ACDC validation, and the retention model design.

TDD plan (strict red-green-refactor):
1. RED proofProposalService.test.ts: ProofProposal (schema SAID, attributes, terms) serialize/deserialize round-trip for QR. GREEN src/core/agent/services/proofProposalService.ts with serializeForQR/deserializeFromQR.
2. RED Scan.test.tsx: proof-request QR decode path. GREEN Scan.tsx handleScanValue detects the proof-request format.
3. RED receivedProofRecord.test.ts: record + retention fields (proverAid, proposalSaid, receivedAt, expiresAt, deleteAfterUse, termsAndConditions) + expiry query. GREEN src/core/agent/records/receivedProofRecord.ts + receivedProofStorage.ts on BasicStorage.
4. RED agent expiry-sweep test: expired proofs deleted on interval. GREEN Agent.scheduleProofExpiryCleanup() wired into the ctor/retry sweeps.
5. RED ipexCommunicationService.test.ts validatePresentation(proof, proposal) -> {valid, errors} reusing schema resolution. GREEN add validatePresentation() (net-new verifier role).
6. RED requestPresentation(connectionId, proposal) returns an exn + persists the proposal + a connection-history entry. GREEN add requestPresentation() (IPEX apply-style); add ConnectionHistoryType PROOF_REQUEST_RECEIVED / PROOF_VERIFIED.
7. RED proposal-composer UI test (canned templates + custom attrs + T&C toggle). GREEN a composer component dispatching requestPresentation.

Reuses: IPEX schema resolution, connection OOBIs, QR scanner, BasicStorage. Net-new: verifier-side validation, the proof archive + retention/T&C. Depends on PAP tick 2dfx for the network channel. Analysis 2026-07-15.
