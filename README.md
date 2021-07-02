# releasetest

Testing GitHub actions release

Goal is to have a release step embedded within a normal CI workflow, and only have it trigger
when a new tag is pushed to the repo.

In theory pushing a new tag should trigger a `push` event, but it didn't work that way for me.
I had to add a `create` action to the `on` section.

This will run the CI whenever a tag or branch is created. To prevent it running when a branch
is created added an if statement to filter out any create action that didn't have a reference 
that started with `refs/tags`

```yaml
    # Only run with create event if new tag, not new branch
    if: github.event_name != 'create' || (github.event_name == 'create' && startsWith(github.ref, 'refs/tags'))
```

So the workflow will only run with `push`/`pull` and `create` (new tag). The release steps are
then selected with another `if` that only matches tags

```yaml
        if: startsWith(github.ref, 'refs/tags/')
```

So this works pretty much perfectly, the only slight issue is that a tag pushed to any branch will
create a release. There is no information that indicates a branch for a tag, I guess they're only
ever associated with a commit hash. Can't have everything.
