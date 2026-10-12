# G17: saveAcdcMetadataRecord (ipexCommunicationService.ts:454-478) assumes every ACDC has a.dt and throws RangeError: Invalid time value on admit when it is absent; a.dt is a signify-ts issuance convention, not part of ACDC, and SEDI schemas forbid it. Evidence: interop/two-wallets #11
kind: debt
tags: interop, g17
created: 2026-10-12T22:01Z

