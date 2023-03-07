<h1 align="center">
  <a href="https://github.com/MadeInPierre/finalynx">
    <img src="./docs/logo_assistant_transparent.png" width="400" />
  </a>
  <br>Finalynx Assistant<br>
</h1>

<div align="center">
  <h4>Minimalistic command-line tool to help you manage your investments</h4>

  <sub>Built with ❤︎ by <a href="https://www.buymeacoffee.com/MadeInPierre">Pierre Laclau</a> and <a href="https://github.com/MadeInPierre/finalynx/graphs/contributors">contributors</a>. Logo generated by <a href="https://midjourney.com">Midjourney</a>.</sub>

  <br><a href="https://pypi.org/project/finalynx/"><img alt="PyPI" src="https://img.shields.io/pypi/v/finalynx?style=flat-square"></a>
  <a href="https://github.com/MadeInPierre/finalynx/actions/workflows/semantic-release.yml"><img alt="GitHub Workflow Status (main)" src="https://img.shields.io/github/actions/workflow/status/madeinpierre/finalynx/semantic-release.yml?branch=main&style=flat-square"></a>
  <a href="https://github.com/MadeInPierre/finalynx/blob/main/LICENSE"><img alt="License" src="https://img.shields.io/github/license/madeinpierre/finalynx?style=flat-square"></a>
  <a href="https://github.com/MadeInPierre/finalynx/milestones"><img alt="GitHub milestones" src="https://img.shields.io/github/milestones/open/madeinpierre/finalynx?color=green&label=milestones&style=flat-square&color=brightgreen"></a>

  <br>
</div>

Finalynx is your "Finary Assistant", a command-line tool to organize your investments portfolio and get automated monthly investment recommendations based on your future life goals.
This tool synchronizes with your [Finary](https://finary.com/) account to show real-time investment values.

Don't have Finary yet? You can sign up using my [referral link](https://finary.com/referral/f8d349c922d1e1c8f0d2) 🌹 (or through the [default](https://finary.com/signup) page).

🇫🇷🥖 Vous pouvez traduire cette page en Français avec votre navigateur (_clic droit > traduire_).

<p align="center">
  <img src="./docs/screenshot.png" width="500" />
</p>

## ✨ Features

1. **✅ Portfolio:** Organize your assets, set targets, and sync with your Finary account.
2. **⏳ Analyzer:** Generate global statistics and graphs to understand each line and folder.
3. **⏳ Simulator:** Define your life goals and events, simulate your portfolio's future.
4. **⏳ Assistant:** Get monthly recommendations on where to invest next to meet your goals.
5. **🙏 Extensions:** Make this tool work for other people's situations. Contributions needed!
6. **🙏 Optional:** Nice but out-of-my-skills or time-consuming features. Any volunteers?

You can check the [current development status](https://github.com/users/MadeInPierre/projects/4). Contributions are warmly welcome!

## 🚀 Installation
If you don't plan on touching the code, simply run (with python >=3.8 and pip installed):
```sh
pip install finalynx  # run again with --upgrade to update
```

And you're done! Now create your own copy of the [`demo.py`](https://github.com/MadeInPierre/finalynx/blob/main/examples/demo.py) example anywhere and run it to make sure everything works. You can now customize it for your own needs 🚀

**Pro Tip 💡:** _Why not setup a script to autorun your config in a new terminal on startup? Could be a nice view_ 🤭

## ⚙️ Usage
The goal is to declare a tree structure of your entire portfolio independently from their host envelopes (e.g. PEA, AV, CTO, etc). Once your entire portfolio strategy is defined here, find the best envelope for each line and add them to your Finary account (manual or automatic sync). Finalynx will fetch each line and display your full portfolio with real-time amounts.

#### Step 1: Portfolio

To create your portfolio, start with a `Portfolio` object which holds a nested list of `Line`, `Folder`, and `SharedFolder` objects:
- `Line` represents each individual investment. Set the `key` parameter as the name shown in your Finary account if different from the display name.
- `Folder` holds a group of lines or subfolders to create a structure.
- `SharedFolder` accepts a `Bucket` object which groups multiple lines as a single object. You can reference the same bucket multiple times in the tree and set different `bucket_amount` for each shared folder. Each folder will only take the specified amount and let the others below use the rest.

Here is an example of a portfolio structure:
```python
# Create a list of Lines that will be considered as a single Line.
my_bucket = Bucket([
  Line('name_in_finary'),
  Line('My Asset 2', key='name_in_finary'),  # change the display name
  # ...
])

# Define your entire portfolio structure
portfolio = Portfolio('My Portfolio', children=[
  # Add a list of `Line`, `Folder`, and `SharedFolder` objects
  Folder('Short term', children=[
    Line('My Asset 3', key='name_in_finary'),
    SharedFolder('My Folder', bucket=my_bucket, bucket_amount=1000),
    # ...
  ]),
  Folder('Long term', children=[
    # `(Shared)Folders` can be displayed as Expanded (default), Collapsed, or as a Line
    Folder('Stocks', display=FolderDisplay.COLLAPSED, children=[
      SharedFolder('My Folder', bucket=my_bucket),  # display what's left in the bucket
      # ...
    ]),
    # ...
  ])
])
```

#### Step 2: Targets
Any node in the tree accepts an optional `target` parameter. See the full list of available targets [here](https://github.com/MadeInPierre/finalynx/blob/main/finalynx/portfolio/targets.py):

```python
Folder('Stocks', target=TargetMin(2000, tolerance=500), children=[
  Line('ETF World', key='Amundi ETF ...', target=TargetRatio(80, tolerance=5)),
  # ... Add other lines with the remaining 20% of the Stocks folder.
])
```

#### Step 3: Run the Assistant
Here is the bare minimum code accepted:
```python
from finalynx import Portfolio, Assistant
portfolio = Portfolio()  # your config here
Assistant(portfolio).run()
```

The `Assistant` class accepts a few options:
```python
Assistant(
    portfolio,
    ignore_orphans=False,  # Ignore fetched lines that you didn't reference in your portfolio.
    force_signin=False,    # Delete your saved credentials and/or cookies session.
    hide_amount=False,     # Hide your portfolio amounts with dots (easier to share).
    hide_root=False,       # Display your portfolio without the root (cosmetic preference).
).run()
```

These options can also be set from the command line, see:
```sh
python your_config.py --help
```

There are other small options here and there, let me know if you're interested (I should write a full documentation). However, you should be good to go with some inspiration taken from [`demo.py`](https://github.com/MadeInPierre/finalynx/blob/main/examples/demo.py).

## 👨‍💻 Feedback & Contributions
This repository is at a very early stage. Unfortunately, I won't have time to make this tool work for everyone by default, but you are welcome to extend this project (or [hire me](https://www.buymeacoffee.com/MadeInPierre/commissions) if you can't develop it yourself). Pull requests, [issues](https://github.com/MadeInPierre/finalynx/issues/new) (🇬🇧 preferably) and [open discussions](https://github.com/MadeInPierre/finalynx/discussions/new) (🇬🇧/🇫🇷) are warmly welcome!

If you would like to contribute to this project, welcome on board and thanks for your interest! 🎉 Please read the [contribution guidelines](https://github.com/MadeInPierre/finalynx/blob/main/CONTRIBUTING.md) to setup the project on your machine and agree on common conventions.

## 📄 License
This project is under the [GPLv3 License](https://github.com/MadeInPierre/finalynx/blob/main/LICENSE) meaning anyone can use, share, extend, and contribute to this project as long as their changes are integrated to this repo or also published using GPLv3. Please contact me for any specific licensing requests.

## 💌 Donations
[<img align="right" src="https://www.mathisplumail.com/wp-content/uploads/2021/04/coffee.png" width="161" />](https://www.buymeacoffee.com/MadeInPierre)
This is a personal project I have fun with on my free time. If you found it useful and wish to support my work, you can [buy me a coffee](https://www.buymeacoffee.com/MadeInPierre)! It would give me the motivation to keep improving it further :smile: Thank you!

Also, big thanks to all contributors 🌹 don't forget to check them out:

<a href="https://github.com/MadeInPierre/finalynx/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=MadeInPierre/finalynx" />
</a>

<!-- Breaking: :boom:

Minor: :sparkles::children_crossing::lipstick::iphone::egg::chart_with_upwards_trend:

Patch: :ambulance::lock::bug::zap::goal_net::alien::wheelchair::speech_balloon::mag::apple::penguin::checkered_flag::robot::green_apple: -->
