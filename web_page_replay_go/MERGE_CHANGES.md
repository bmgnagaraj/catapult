Summary of changes to fix build errors and make httparchive compile

Files changed:

1) src/httparchive.go
   - Converted attempts to define methods on an external type into package-level functions.
   - Replaced all uses of `HttpArchiveConfig` as a local type with the package-qualified type `webpagereplay.HttpArchiveConfig` where constructed.
   - Replaced calls to the former method `cfg.requestEnabled(...)` with `requestEnabled(cfg, ...)`.
   - Updated all references to config fields to use exported names (Method, Host, FullPath, StatusCode, DecodeResponseBody, SkipExisting, OverwriteExisting, InvertMatch).

2) src/webpagereplay/httparchive_config.go
   - Exported config struct fields by renaming them to uppercase (Method, Host, FullPath, StatusCode, DecodeResponseBody, SkipExisting, OverwriteExisting, InvertMatch).
   - Updated cli flag Destination pointers to reference the exported fields (e.g., &cfg.DecodeResponseBody).
   - Left the flag helper methods (`RequestFilterFlags`, `DefaultFlags`, `AddFlags`, `TrimFlags`) in the `webpagereplay` package.

Rationale:
- Go prohibits adding methods to types declared in another package; the original code attempted to define methods on `webpagereplay.HttpArchiveConfig` from `main` package. I converted the method into a local function and made fields accessible where needed.
- Exporting fields is necessary so code in `main` (or any other package) can read/modify them and so the `cli` package can use them as flag destination pointers.

Patch file:
- The full git diff is stored in `httparchive_fix.patch` located in this folder. Apply it from the repository root with:

  git apply web_page_replay_go/httparchive_fix.patch

Notes / alternatives:
- If you prefer to keep fields unexported for encapsulation, a preferable alternative is to add exported accessor methods or move `requestEnabled` and related logic into the `webpagereplay` package so the methods can be defined on the local type.
- Another option is to move `httparchive.go` into the `webpagereplay` package; that would also allow defining methods on the type directly without exporting fields.

Next steps I can perform for you on request:
- Rework to keep fields unexported and add proper accessor methods.
- Move helper functions back into `webpagereplay` to preserve original API design.
- Create a proper commit and a branch with the changes and run `go vet`/`golint` if desired.

If you'd like any of those, tell me which and I'll proceed.
