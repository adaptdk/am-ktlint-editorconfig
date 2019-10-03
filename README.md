# am-ktlint-editorconfig
For disabling ktlint rules we don't need being checked and formatted.

## Installation
First install [ktlint](https://github.com/pinterest/ktlint#installation) and add [ktlint-gradle](https://github.com/JLLeitschuh/ktlint-gradle) to your project.
Then use the gradle command to add a pre-commit to your git hooks.

```bash
$ ./gradlew addKtlintFormatGitPreCommitHook
```

After that go to the root of the project and run this command to open the pre-commit in TextEdit application (Only Mac):

```bash 
$ open -a "TextEdit" .git/hooks/pre-commit
```

Then add the curl to the file:

```sh
curl -s -L "https://raw.githubusercontent.com/adaptdk/am-ktlint-editorconfig/master/.editorconfig" > .editorconfig
```

The end out put should be like this:

```sh
#!/bin/sh
set -e
######## KTLINT-GRADLE HOOK START ########
curl -s -L "https://raw.githubusercontent.com/adaptdk/am-ktlint-editorconfig/master/.editorconfig" > .editorconfig

CHANGED_FILES="$(git --no-pager diff --name-status --no-color --cached | awk '$1 != "D" && $2 ~ /\.kts|\.kt/ { print $2}')"

if [ -z "$CHANGED_FILES" ]; then
    echo "No Kotlin staged files."
    exit 0
fi;

echo "Running ktlint over these files:"
echo "$CHANGED_FILES"

# ./gradlew --quiet ktlintFormat -PinternalKtlintGitFilter="$CHANGED_FILES"
ktlint -F $CHANGED_FILES

echo "Completed ktlint run."

echo "$CHANGED_FILES" | while read -r file; do
    if [ -f $file ]; then
        git add $file
    fi
done

echo "Completed ktlint hook."
######## KTLINT-GRADLE HOOK END ########

```
