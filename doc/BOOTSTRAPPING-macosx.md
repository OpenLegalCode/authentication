### First-time setup

Install the Xcode compilers: Download
[XCode.app](https://itunes.apple.com/us/app/xcode/id497799835), then go to
Preferences -> Downloads -> and ensure that Command Line Tools is installed.
If it doesn't appear in that list, then run this command in a Terminal:

```shell
xcode-select --install
```


Install [Homebrew](http://brew.sh/). (It's safe to run the following command
even if you do have brew installed, it’ll just warn you about it. Don’t do the
"reinstall" step if you do get that warning.)

```shell
ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"
```

Make sure Homebrew is up to date (so it knows about the latest software
packages) and then install Python and GnuPG.

```shell
brew update
brew install gnupg2
brew install python
```

(If you get a `Error: python-2.X.X already installed`, you should be fine.
If you’d like to upgrade Python just to be safe, do `brew upgrade python`.)

At this point, close your terminal window and open a new one. Test that
the correct version of Python is installed.

```shell
which python2
# should be "/usr/local/bin/python2"
which pip
# should be "/usr/local/bin/pip"
```

If you don’t get the proper values above, make sure `/usr/local/bin`
is in your `$PATH` before any other entry; edit your .bashrc, .bash_profile,
.zshrc, or whatever your shell of choice uses, and add the following to the
bottom of the file:

```shell
export PATH=/usr/local/bin:$PATH
```

---

Now, the fun parts. We’ll use `virtualenvwrapper`, which is a way of
setting up "virtual environments" for the project. A Python virtual
environment basically keeps an isolated set of Python libraries that
don’t interfere with system libraries or libraries that other Python apps
use. (It’s similar to Ruby bundles, with a bit more isolation and manual
steps.)

```shell
pip install virtualenvwrapper
```

Once again, edit your .bashrc, .bash_profile, .zshrc, or however you configure
your shell. Add the following lines to the bottom of it: (This is a variation
on [this fine tutorial](http://www.jeffknupp.com/blog/2013/12/18/starting-a-django-16-project-the-right-way/).)

```shell
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/Code
export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python2
source /usr/local/bin/virtualenvwrapper.sh
```

You may replace `$HOME/Code` with wherever you put project directories or
repos you are working on.

Again, close your terminal window and open a new one so that these new
configuration changes take.

---

Now create a "virtual environment" for this project and clone this repo:

```shell
mkvirtualenv odi-authentication
cd $PROJECT_HOME
git clone git@github.com:unitedstates/authentication.git odi-authentication
cd odi-authentication
```

Install some dependencies:

```shell
pip install -r requirements.txt
```

---

Now you’ll want to configure the server. `cd` to the `authentication` folder.

Inside this `authentication` folder, copy `local_settings.py.example` to
`local_settings.py`. Then edit `local_settings.py`.

* Make `SECRET_KEY` and `GNUPG_PASSPHRASE` strings that are somewhat
  random. Use your favorite password generator or just bang on the
  keyboard a bit.
* Uncomment `GNUPG_BINARY` and set it to `"/usr/local/opt/gnupg2/bin/gpg2"`

Now run the following command:

```shell
python manage.py gpginit
```

It will take a while, but you will now get a value for `GNUPG_IDENTITY`
which you should throw into your `local_settings.py`.

---

Now set up an initial database. This is stored in
`db.sqlite3`, a [SQLite](https://sqlite.org/) database.
It'll ask you to create an initial admin user: you should do this, so you
can log into the site.

```shell
python manage.py migrate
```

Now that that's done, you should be able to run the local server by running
the following command…

```shell
python manage.py runserver
```

…and opening your web browser to `http://127.0.0.1:8000/`.

### Running the server normally

Now that you’ve set up the app, you don’t need to do most of the song and dance
as above. Just activate the environment we set up and change to the directory
of the repo.

```shell
workon odi-authentication
cd $PROJECT_HOME/odi-authentication
```

And you can run the server as before:

```shell
cd authentication
python manage.py runserver
```

If someone’s made an update to the `requirements.txt` since you last checked
out, just do a `pip install -r requirements.txt` after activating the
project’s virtual environment (`workon odi-authentication`) and going into
the repo directory.

If someone’s made an update to any `models.py` files since you last checked out,
activate the virtual environment and run `python manage.py migrate` once again.
