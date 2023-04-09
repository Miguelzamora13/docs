# GitHub Docs <!-- omit in toc -->

This repository contains the documentation website code and Markdown source files for [docs.github.com](https://docs.github.com).

https://en.wikipedia.org/wiki/Type_I_and_type_II_errors) arising from systemic conditions external to your proposed changes, consider starting with an experiment in which both the `try` and `use` blocks invoke the control method. Then proceed with introducing a candidate.

### Finishing an experiment

As your candidate behavior converges on the controls, you'll start thinking about removing an experiment and using the new behavior.

* If there are any ignore blocks, the candidate behavior is *guaranteed* to be different. If this is unacceptable, you'll need to remove the ignore blocks and resolve any ongoing mismatches in behavior until the observations match perfectly every time.
* When removing a read-behavior experiment, it's a good idea to keep any write-side duplication between an old and new system in place until well after the new behavior has been in production, in case you need to roll back.

## Breaking the rules

Sometimes scientists just gotta do weird stuff. We understand.

### Ignoring results entirely

Science is useful even when all you care about is the timing data or even whether or not a new code path blew up. If you have the ability to incrementally control how often an experiment runs via your `enabled?` method, you can use it to silently and carefully test new code paths and ignore the results altogether. You can do this by setting `ignore { true }`, or for greater efficiency, `compare { true }`.

This will still log mismatches if any exceptions are raised, but will disregard the values entirely.

### Trying more than one thing

It's not usually a good idea to try more than one alternative simultaneously. Behavior isn't guaranteed to be isolated and reporting + visualization get quite a bit harder. Still, it's sometimes useful.

To try more than one alternative at once, add names to some `try` blocks:

```ruby
require "scientist"

class MyWidget
  include Scientist

  def allows?(user)
    science "widget-permissions" do |e|
      e.use { model.check_user(user).valid? } # old way

      e.try("api") { user.can?(:read, model) } # new service API
      e.try("raw-sql") { user.can_sql?(:read, model) } # raw query
    end
  end
end
```

When the experiment runs, all candidate behaviors are tested and each candidate observation is compared with the control in turn.

### No control, just candidates

Define the candidates with named `try` blocks, omit a `use`, and pass a candidate name to `run`:

```ruby
experiment = MyExperiment.new("various-ways") do |e|
  e.try("first-way")  { ... }
  e.try("second-way") { ... }
end

experiment.run("second-way")
```

The `science` helper also knows this trick:

```ruby
science "various-ways", run: "first-way" do |e|
  e.try("first-way")  { ... }
  e.try("second-way") { ... }
end
```

#### Providing fake timing data

If you're writing tests that depend on specific timing values, you can provide canned durations using the `fabricate_durations_for_testing_purposes` method, and Scientist will report these in `Scientist::Observation#duration` instead of the actual execution times.

```ruby
science "absolutely-nothing-suspicious-happening-here" do |e|
  e.use { ... } # "control"
  e.try { ... } # "candidate"
  e.fabricate_durations_for_testing_purposes( "control" => 1.0, "candidate" => 0.5 )
end
```

`fabricate_durations_for_testing_purposes` takes a Hash of duration values, keyed by behavior names.  (By default, Scientist uses `"control"` and `"candidate"`, but if you override these as shown in [Trying more than one thing](#trying-more-than-one-thing) or [No control, just candidates](#no-control-just-candidates), use matching names here.)  If a name is not provided, the actual execution time will be reported instead.

GitHub's Docs team works on pre-production content in a private repo that regularly syncs with this public repo.

Use the table of contents icon <img src="/contributing/images/table-of-contents.png" width="25" height="25" /> on the top left corner of this document to get to a specific section of this guide quickly.

## Contributing

See [the contributing guide](CONTRIBUTING.md) for detailed instructions on how to get started with our project.

We accept different [types of contributions](https://github.com/github/docs/blob/main/contributing/types-of-contributions.md), including some that don't require you to write a single line of code.

On the GitHub Docs site, you can click the make a contribution button at the bottom of the page to open a pull request for quick fixes like typos, updates, or link fixes.

<img src="./contributing/images/contribution_cta.png" width="400">

For more complex contributions, you can open an issue using the most appropriate [issue template](https://github.com/github/docs/issues/new/choose) to describe the changes you'd like to see.

If you're looking for a way to contribute, you can scan through our [existing issues](https://github.com/github/docs/issues) for something to work on. When ready, check out [Getting Started with Contributing](/CONTRIBUTING.md) for detailed instructions.

### Join us in discussions

We use GitHub Discussions to talk about all sorts of topics related to documentation and this site. For example: if you'd like help troubleshooting a PR, have a great new idea, or want to share something amazing you've learned in our docs, join us in the [discussions](https://github.com/github/docs/discussions).

### And that's it!

If you're having trouble with your GitHub account, contact [Support](https://support.github.com/contact).

That's how you can easily become a member of the GitHub Docs community. :sparkles:

## READMEs

In addition to the README you're reading right now, this repo includes other READMEs that describe the purpose of each subdirectory in more detail:

- [content/README.md](content/README.md)
- [content/graphql/README.md](content/graphql/README.md)
- [content/rest/README.md](content/rest/README.md)
- [contributing/README.md](contributing/README.md)
- [data/README.md](data/README.md)
- [data/reusables/README.md](data/reusables/README.md)
- [data/variables/README.md](data/variables/README.md)
- [components/README.md](components/README.md)
- [lib/liquid-tags/README.md](lib/liquid-tags/README.md)
- [middleware/README.md](middleware/README.md)
- [script/README.md](script/README.md)
- [stylesheets/README.md](stylesheets/README.md)
- [tests/README.md](tests/README.md)

## License

The GitHub product documentation in the assets, content, and data folders are licensed under a [CC-BY license](LICENSE).

All other code in this repository is licensed under the [MIT license](LICENSE-CODE).

When using the GitHub logos, be sure to follow the [GitHub logo guidelines](https://github.com/logos).

## Thanks :purple_heart:

Thanks for all your contributions and efforts towards improving the GitHub documentation. We thank you for being part of our :sparkles: community :sparkles:!
