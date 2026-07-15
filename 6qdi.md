# Feature: full localization / i18n (CJK, EU langs, ru/el/he/ar incl. RTL)
kind: idea
created: 2026-07-15T18:41Z

- 2026-07-15T18:42Z Maturity: foundation only. i18next + react-i18next wired in src/i18n.ts with ~1404 keys across 6 namespaces under src/locales/en/, but lng:"en" is hardcoded, the language-detector is registered yet inert, no other locales exist, and there is no switcher or persistence. RTL unsupported: no document.dir, and 156+ SCSS files use physical left/right properties. Formatters in src/ui/utils/formatters.ts hardcode en-GB/en-US.
Target langs: zh/ja/ko, fr/de/es/it/pt, ru, el, he, ar (he + ar are RTL).

TDD plan (strict red-green-refactor):
1. RED: i18n.test.ts changeLanguage(fr) updates i18n.language and t() output; selection persists via Redux/localStorage.
2. GREEN: add src/store/reducers/localeSlice.ts; remove hardcoded lng; init from persisted/detected locale; languageChanged dispatches setLocale.
3. RED: rtl.test.ts getIsRTL(ar|he) is true; document.dir flips to rtl for ar/he and ltr otherwise.
4. GREEN: add src/ui/utils/rtlHelpers.ts and wire into languageChanged; begin the physical-to-logical CSS migration (margin-inline etc.).
5. RED: formatters.test.ts locale-aware date/number for ar vs en-GB.
6. GREEN: thread i18n.language through formatters.ts.
7. RED: assert all target locale bundles register and load without error.
8. GREEN: scaffold src/locales/<locale>/ bundles + register them; add an ESLint no-hardcoded-strings guard; add a LanguageSwitcher settings UI.
Separate the mechanical enablement (steps 1-8) from the large translation-content effort (populating ~1404 keys x 12 langs, deferred to a Crowdin-style workflow). Analysis 2026-07-15.
