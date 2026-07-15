# Feature: white-label / re-branding (swappable theme, logo, app name)
kind: idea
created: 2026-07-15T18:41Z

- 2026-07-15T18:42Z Feasibility: HIGH. Colors are centralized as CSS custom properties in src/ui/styles/colors.scss; fonts in src/ui/styles/style.scss. Scattered anchors: splash color hardcoded in capacitor.config.ts; app name in capacitor.config.ts + android/app/src/main/res/values/strings.xml + ios/App/App/Info.plist; logo/intro assets in src/assets/ and src/ui/components/Intro/Intro.tsx; product copy in src/locales/en/en.json.

TDD plan (strict red-green-refactor):
1. RED: src/config/brand/brand.config.test.ts asserts resolveBrand(id) returns BrandConfig {id, name, themeColors hex map, fontFamily, logoPath, splashColor, productNameKey} from a brands.json registry; unknown id falls back to veridian.
2. GREEN: add src/config/brand/brand.config.ts + brands.json + useBrandConfig hook.
3. RED: integration test that the brand theme is applied at runtime via document.documentElement.style.setProperty on the --ion-color vars, and that a component reads the CSS var not a hardcoded hex.
4. GREEN: apply brand tokens at bootstrap (index.tsx / App.tsx).
5. RED: test capacitor.config.ts resolves appName/appId/splashColor from a BRAND_ID env var.
6. GREEN: parameterize capacitor.config.ts + webpack DefinePlugin brand injection; add a build:brand npm script.
7. Native: Android product flavor + iOS build-setting for per-brand app name; per-brand assets under src/assets/brands/<id>/ regenerated via capacitor-assets.
Guard: a unit test that fails if new hardcoded hex colors appear outside colors.scss.

Effort: theme + name swap is small; native per-brand build + asset pipeline is the bulk of the work. Analysis 2026-07-15.
