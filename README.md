# GitHooks
Git Hooks intended to simplify development efforts on [vutc-laravel](https://github.com/vutechcrew/vutc-laravel/). These are also suitable for use in any PHP project that uses [Composer](https://getcomposer.org/) as a dependency manager and [dotenv](https://github.com/vlucas/phpdotenv) files for environment variable handling.

## Using these hooks
Using githooks is easy - copy them into the hidden `.git/hooks` folder in your repository. They will automatically run when the operation described in their name is executed. the `.git/hooks` directory is not tracked by Git, so these hooks will not automatically sync to the GitHub (or other) server, nor to any other developers' clones of whatever repository you copy them into.

## How do Githooks Work?
Githooks are triggered automatically by Git's internal operations. Regardless of your environment (Windows/Linux/macOS), these scripts are written in a common language (in this case, shell scripts, although python is also a good candidate). For details on how these hooks work, as well as what other options are available for further development, checkout the 
[githooks docs](https://git-scm.com/docs/githooks).

## Githooks in this Repo
There are currently only two githooks in this repo, and both do very similar things:

### Post-Merge
The [post-merge](post-merge) githook fires whenever a merge ocurrs. Behind the scenes, this happens every time the repo gets pulled, making it ideal for updating things that may be affected by changes in the repo but that aren't directly tracked in Git. Two things come to mind: the vendor folder and the .env file. 

In vutc-laravel (and many other projects), the vendor folder is gitignored. Since we don't maintain that code, there's no sense in having it clutter up the repo and all of our pull requests. Instead, we track the composer.json and composer.lock files, which Composer uses to install the dependencies. When these files change, the command `composer install` needs to be fired to install any new dependencies, as well as ensure all existing dependencies are at the correct version. This script monitors these files for changes, and will automatically run composer install if it detects modifications from the pre-merge state.

Laravel harnesses the [phpdotenv library](https://github.com/vlucas/phpdotenv) to manage environment variables. These variables tend to be different on production clones and development clones, so they are typically gitignored (as is the case with vutc-laravel). However, it's often still desired to track the variables for these two configuration. vutc-laravel does so by maintaining two files: a .env.production and a .env.development. Unfortunately, laravel only looks for a single .env file, and doesn't know how to handle these additional extensions. To rectify this, the developer needs to rename the .env.development file to .env, and monitor it for changes. This script also automates this process, by copying the .env.development as .env if it detects changes, and moving the .env (if it exists)  to .env.old. 

This hook is adapted from [ stefansundin/git-bundle-hook](https://gist.github.com/stefansundin/82051ad2c8565999b914)

### Post-Checkout
The [post-checkout](post-checkout) githook does exactly the same thing as the [post-merge](#post-merge) one described above, but on branch switches or file reversions instead of pulls. The code differs slightly to accomodate this, but the functionality is unchanged.

This hook is also adapted from [ stefansundin/git-bundle-hook](https://gist.github.com/stefansundin/82051ad2c8565999b914)

## Usage Notes
These githooks are designed to be relatively robust, however they do expect that .env.development, composer.json, and composer.lock exist. They also assume that the .gitignore for the target repository has been configured to ignore the .env.old file, as that's not something that most developers will want to track.

## Security
If you discover a security vulnerability within this set of githooks, please send an email to [webmaster@vutc.com](mailto:webmaster@vutc.com). Please do not present security concerns publicly. All security vulnerabilities will be promptly addressed.

## License
vutechcrew/githooks is licensed under [The GNU General Public License v3](https://www.gnu.org/licenses/gpl-3.0.en.html).