<!-- reviewed-through: <sha> (<YYYY-MM-DD HH:MM>) -->
# Lessons from merged work

Each review reads the commits merged since the last one for what had to be fixed again, what
shouldn't have merged and what was verified too thinly. Each lesson is a rule, then why, then
the evidence. "Seen" counts the days a review found it here. A lesson seen on 3 or more days
moves into <standards file>.

The lessons marked "inherited" come from another project whose code agents write, review, merge
and release. They start at Seen 0× and carry no evidence from this repo until a review finds one
here.

## Reading the report

**Reproduce the reporter's exact case, on the surface they use, before you call it fixed.**
- Use their provider, config, platform and interface (GUI, TUI or CLI). Check their goal, not a
  step on the way to it.
- One report came back four times. Each round fixed only the case in the screenshot.
- Seen 0× (inherited).

**Build fixtures from the reporter's literal bytes.**
- An invented payload "proved" a parser fix. The real input had another shape, and the reporter
  came back.
- For parsers of model or vendor output, collect several real failing inputs first. Truncated and
  empty bodies go in the tests.
- Seen 0× (inherited).

**When an upstream refuses your client but serves its own client on the same account, diff every
header of the failing request against the official client's. Do it before you add retries or
fallbacks.**
- Four releases each added a fallback. The cause was one missing header.
- Seen 0× (inherited).

**Decide whether a refusal is about the account, the model or the request content before you
penalize an account.**
- A content refusal counted as an account fault rested every account of one provider for days.
- Test that the same request fails the same way on a second account.
- Seen 0× (inherited).

**Fix what the error is about, as well as how it is worded.**
- A relabelled error left its cause in place, and the user's requests still failed.
- When a report shows a wrong label, also ask why the request failed. Fix that, or point the user
  to the fix in the message itself.
- Seen 0× (inherited).

## Fix the class, not the sample

**Scope a vendor rule to that vendor.**
- A limit added for one vendor's model capped every model.
- Check the claim against the vendor's source or docs, and add a test that another vendor's case
  is unchanged.
- Seen 0× (inherited).

**A restriction covers every route that reaches the thing it guards.**
- A limit on one endpoint left two other endpoints to the same resource open.
- Seen 0× (inherited).

## Security and privacy

**Scope a privacy or security exemption by where the value sits in the protocol, never by a
key's name.**
- An exemption for any object with a "signature" key let user data in tool arguments go out
  unmasked.
- Test the exemption with the protected key placed where user data goes: tool arguments, message
  text, a nested object.
- Seen 0× (inherited).

**A security gap that a doc names is an open bug.**
- A design doc described a DNS-rebinding hole as current behavior. It stayed exploitable until a
  review reproduced it.
- When you write such a sentence, file an issue in the same commit.
- Seen 0× (inherited).

**What telemetry sends is an allow-list. A change to what is sent updates every place that
discloses it.**
- Update the privacy text in the READMEs and docs, in every language, in the same commit.
- Seen 0× (inherited).

## The user's own files and settings

**A value the user set themselves wins over your default.**
- Before you write a setting, list everywhere the tool reads it from. Test with user-set values
  present.
- Seen 0× (inherited).

**A failed read, parse or hash means "unknown". It never means "empty" or "equal".**
- A file that didn't parse was read as "no accounts", and the next write saved that.
- Keep what was read before. Copy a bad file aside before you write over it. Back up before you
  move a whole file. When a shared reader is wrong, fix the shared reader.
- Seen 0× (inherited).

**Before you delete or overwrite a live credential, find every record that could match the
same key.**
- Matching accounts by display name deleted the credentials of two accounts that shared one name.
- Seen 0× (inherited).

**When you hand users a writable copy, test "user edits it, then sync".**
- A sync on the next start undid the user's own edits.
- Seen 0× (inherited).

**Take another app's field types and merge rules from that app's own data, code or validator.**
- A guessed timestamp unit hung another app's page for two days.
- Seen 0× (inherited).

## Verification

**Run the tests on every platform that CI only compiles for.**
- CI only compiled the tests for one OS, so failures on that OS showed nowhere for days.
- Seen 0× (inherited).

**A test stays off the real network and real third-party pages.**
- A test that opened a vendor's page hung on its bot challenge.
- Serve the page locally from saved bytes. Break the change and watch where the test's requests go.
- Seen 0× (inherited).

**A test that reads a dependency's source makes sure the dependency is on disk.**
- CI's fresh cache had not unpacked it, and main was red on two OSes.
- Seen 0× (inherited).

**A check that a value is gone looks past text your code generates.**
- A substring check failed when a random placeholder suffix spelled the word.
- Seen 0× (inherited).

**Reproduce on CI's toolchain version.**
- A crash that only CI's older compiler had could not be reproduced locally.
- Seen 0× (inherited).

## Concurrency and tests

**Release a lock before any send or call that can block on a receiver that needs it. Set shared
state before the handoff that lets another thread see it.**
- A send into a full buffer under the reader's lock hung CI for 20 minutes. A test that fills the
  buffer first catches it every time.
- A struct copied by value that holds a map is not a copy.
- Seen 0× (inherited).

**After a test times out, look for its orphaned processes.**
- An orphaned test binary spun at 96% CPU for 30 minutes. Check `ps` for it. A timeout is not a
  hang until its stack traces show one.
- Seen 0× (inherited).

**A test named for a guarantee fails when the guarantee breaks.**
- Before merging, break the guarantee by hand and watch the test go red.
- Seen 0× (inherited).

## Red tests and releases

**Claim a red before you fix it.**
- Two sessions fixed the same flaky test in the same minute, twice.
- Before you start, `git log origin/main` for a commit that names the run or test. Then say on an
  issue, or file one, that you are on it.
- Seen 0× (inherited).

**"The same on main" means on `origin/main` just fetched, with its sha named.**
- A commit called a red "the same on main" against an hours-old main where the test had since been
  fixed. On the real main it passed 3 of 3.
- Seen 0× (inherited).

**Tag only after a CI run on that commit has finished green. A cancelled run is not a pass.**
- Hundreds of tags in two days left no CI run to finish. A failure on one OS stayed red across
  15 releases.
- Batch fixes. Before you name a version to a reporter, check that its release assets exist.
  Before you list a package, check that it resolves in its registry.
- Seen 0× (inherited).

## Merging and closing

**Merge a PR only when its own new tests pass and every problem a review listed is fixed or
filed.**
- Seen 0× (inherited).

**Keep an issue open until the reporter's case works. Reopen it when they say it doesn't.**
- Leave the decisions the owner hasn't made to the owner, including "not planned".
- Seen 0× (inherited).

**One commit, one goal.**
- A fix found on the way goes in its own commit.
- Seen 0× (inherited).

**A large commit has a body: what changed, why, and what was run.**
- Seen 0× (inherited).

**Each session works in its own worktree.**
- Two sessions that shared one checkout committed each other's staged files under the wrong
  subjects. After you commit, `git show --stat HEAD` lists only your files.
- Seen 0× (inherited).

## Moved to the standards
