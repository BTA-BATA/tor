
Putting out a new release
-------------------------

Here are the steps Roger takes when putting out a new Tor release:

1. Use it for a while, as a client, as a relay, as a hidden service,
   and as a directory authority. See if it has any obvious bugs, and
   resolve those.

   As applicable, merge the `maint-X` branch into the `release-X` branch.

2. Gather the `changes/*` files into a changelog entry, rewriting many
   of them and reordering to focus on what users and funders would find
   interesting and understandable.

   1. Make sure that everything that wants a bug number has one.
      Make sure that everything which is a bugfix says what version
      it was a bugfix on.

   2. Concatenate them.

   3. Sort them by section. Within each section, sort by "version it's
      a bugfix on", else by numerical ticket order.

   4. Clean them up:

      Standard idioms:
      `Fixes bug 9999; bugfix on 0.3.3.3-alpha.`

      One space after a period.

      Make stuff very terse

      Make sure each section name ends with a colon

      Describe the user-visible problem right away

      Mention relevant config options by name.  If they're rare or unusual,
      remind people what they're for

      Avoid starting lines with open-paren

      Present and imperative tense: not past.

      'Relays', not 'servers' or 'nodes' or 'Tor relays'.

      "Stop FOOing", not "Fix a bug where we would FOO".

      Try not to let any given section be longer than about a page. Break up
      long sections into subsections by some sort of common subtopic. This
      guideline is especially important when organizing Release Notes for
      new stable releases.

      If a given changes stanza showed up in a different release (e.g.
      maint-0.2.1), be sure to make the stanzas identical (so people can
      distinguish if these are the same change).

   5. Merge them in.

   6. Clean everything one last time.

   7. Run `./scripts/maint/format_changelog.py` to make it prettier.

3. Compose a short release blurb to highlight the user-facing
   changes. Insert said release blurb into the ChangeLog stanza. If it's
   a stable release, add it to the ReleaseNotes file too. If we're adding
   to a release-0.2.x branch, manually commit the changelogs to the later
   git branches too.

   If you're doing the first stable release in a series, you need to
   create a ReleaseNotes for the series as a whole.  To get started
   there, copy all of the Changelog entries from the series into a new
   file, and run `./scripts/maint/sortChanges.py` on it.  That will
   group them by category.  Then kill every bugfix entry for fixing
   bugs that were introduced within that release series; those aren't
   relevant changes since the last series.  At that point, it's time
   to start sorting and condensing entries.  (Generally, we don't edit the
   text of existing entries, though.)

4. In `maint-0.2.x`, bump the version number in `configure.ac` and run
   `scripts/maint/updateVersions.pl` to update version numbers in other
   places, and commit.  Then merge `maint-0.2.x` into `release-0.2.x`.

   (NOTE: To bump the version number, edit `configure.ac`, and then run
   either `make`, or `perl scripts/maint/updateVersions.pl`, depending on
   your version.)

5. Make dist, put the tarball up somewhere, and tell `#tor` about it. Wait
   a while to see if anybody has problems building it. Try to get Sebastian
   or somebody to try building it on Windows.

6. Get at least two of weasel/arma/Sebastian to put the new version number
   in their approved versions list.

7. Sign the tarball, then sign and push the git tag:

        gpg -ba <the_tarball>
        git tag -u <keyid> tor-0.2.x.y-status
        git push origin tag tor-0.2.x.y-status

8. scp the tarball and its sig to the dist website, i.e.
   `/srv/dist-master.torproject.org/htdocs/` on dist-master. When you want
   it to go live, you run "static-update-component dist.torproject.org"
   on dist-master.

   Edit `include/versions.wmi` and `Makefile` to note the new version.

9. Email the packagers (cc'ing tor-assistants) that a new tarball is up.
   The current list of packagers is:

       - {weasel,gk,mikeperry} at torproject dot org
       - {blueness} at gentoo dot org
       - {paul} at invizbox dot io
       - {ondrej.mikle} at gmail dot com
       - {lfleischer} at archlinux dot org
       - {tails-dev} at doum dot org

10. Add the version number to Trac.  To do this, go to Trac, log in,
    select "Admin" near the top of the screen, then select "Versions" from
    the menu on the left.  At the right, there will be an "Add version"
    box.  By convention, we enter the version in the form "Tor:
    0.2.2.23-alpha" (or whatever the version is), and we select the date as
    the date in the ChangeLog.

11. Forward-port the ChangeLog.

12. Wait up to a day or two (for a development release), or until most
    packages are up (for a stable release), and mail the release blurb and
    changelog to tor-talk or tor-announce.

   (We might be moving to faster announcements, but don't announce until
   the website is at least updated.)

13. If it's a stable release, bump the version number in the `maint-x.y.z`
    branch to "newversion-dev", and do a `merge -s ours` merge to avoid
    taking that change into master.  Do a similar `merge -s theirs`
    merge to get the change (and only that change) into release.  (Some
    of the build scripts require that maint merge cleanly into release.)
