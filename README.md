# project-templates

Copier template for new projects: direnv + Nix devshell + justfile, optional
Python/uv flavour, optional GitHub repo creation.

## Requirements

`copier` (>= 9.2), `nix` with flakes, `direnv`, `git`, and `gh` (only if
creating a GitHub repo). Install copier globally, e.g. `nix profile install nixpkgs#copier`.

## Usage

```bash
# push this directory to GitHub first, then:
mkdir myproj && cd myproj
copier copy --trust gh:<you>/project-templates .

# or from a local checkout
copier copy --trust ~/src/project-templates .

# non-interactive
copier copy --trust --defaults \
  -d language=python -d create_github=true -d github_visibility=private \
  gh:<you>/project-templates myproj

# pull later template improvements into an existing project
copier update --trust
```

`--trust` is required because the template runs post-generation tasks
(git init, nix flake lock, uv sync, direnv allow, commit, gh repo create).
Use `--skip-tasks` to generate files only.

`copier update` needs the template to be a git repo with tags; tag releases
(`git tag v0.1.0`) so projects can update to them.

## Notes

- Nix flakes only see git-tracked files, so tasks stage everything before
  running `nix flake lock` / `nix develop`.
- Python uses nixpkgs' interpreter (`UV_PYTHON`, `UV_PYTHON_DOWNLOADS=never`)
  so uv never downloads its own, which avoids dynamic-linking issues on NixOS.
- Add a new flavour by adding a choice to `language` in `copier.yml` and
  `{% if language == '...' %}` blocks / conditional filenames in `template/`.
