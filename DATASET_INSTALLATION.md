# Dataset Installation

This repository expects the VOC dataset to be stored locally in two folders at the repository root:

- [test](test)
- [trainval](trainval)

The dataset archives are shared separately on OneDrive and are not committed to Git.

## Steps

1. Download `test.zip` from the shared OneDrive location.
2. Download `trainval.zip` from the shared OneDrive location.
3. Extract both archives in the repository root.
4. Confirm that the extracted folders are named [test](test) and [trainval](trainval).

## Expected Layout

The repository should look like this after installation:

```text
VOC_MAIN/
  test/
  trainval/
  artifacts/
  README.md
  DATASET_INSTALLATION.md
```

## Notes

- Do not commit the archive files or extracted dataset folders.
- The repository already ignores [test](test), [trainval](trainval), and `*.zip` files through [.gitignore](.gitignore).
- If the OneDrive link changes, update this file so the team has a single source of truth.
