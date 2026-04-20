# Contributing

Contributions are welcome - bug fixes, improvements to existing manifests, and new optional resources.

## What belongs here

- Fixes to existing manifests (security hardening, best practice updates, API version bumps)
- Improvements to the README or documentation
- New resources that fit the "pure Kubernetes baseline" scope

If you're unsure whether something fits, open an issue first.

## How to contribute

1. Fork the repo and create a branch
2. Make your changes
3. Run the ASCII check: `python3 check_ascii.py`
4. Open a pull request with a short description of what changed and why

## Guidelines

- Keep manifests minimal - every field should be there for a reason
- If you add a resource that requires a CRD or external controller, note it in the `Requires` column of the README table
- Security defaults should not be weakened without a strong justification

## Reporting issues

Open a [GitHub issue](https://github.com/ryanparsa/kubernetes-namespace-template/issues) for bugs, outdated API versions, or suggestions.
