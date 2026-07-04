# MindBridge

A community platform where people share their struggles, find others who've
been through the same thing, and discover how those people found their way.

---

## Run it locally

```bash
# 1. Go into the project (the folder that has manage.py in it)
cd mindbridge

# 2. (Recommended) create a virtual environment
python3 -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Set up the database
python manage.py makemigrations
python manage.py migrate

# 5. Seed some demo content (8 categories, 6 real-feeling problems + solutions)
python manage.py seed_data

# 6. Create your own admin account
python manage.py createsuperuser

# 7. Run the server
python manage.py runserver
```

Then open **http://127.0.0.1:8000** in your browser.

Demo login (created by `seed_data`): `demo_user` / `demopassword123`

---

## Project layout

```
mindbridge/
├── manage.py                  ← Django entry point, run all commands from here
├── requirements.txt
├── db.sqlite3                 ← created after you run migrate
│
├── mindbridge/                 the project package (settings, root urls)
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
│
├── core/                       landing page, about, how-it-works, contact
│   ├── views.py
│   ├── urls.py
│   └── templates/core/
│
├── accounts/                   register, login, public profile
│   ├── models is just django.contrib.auth.User — no custom model needed
│   ├── forms.py
│   ├── views.py
│   ├── urls.py
│   └── templates/accounts/
│
├── problems/                    the heart of the app
│   ├── models.py               Category, Problem, Solution, Comment, SolutionVote
│   ├── forms.py
│   ├── views.py                search, filtering, voting, accepting solutions
│   ├── urls.py
│   ├── admin.py
│   ├── templates/problems/
│   └── management/commands/
│       └── seed_data.py        run with: python manage.py seed_data
│
├── static/
│   ├── css/
│   │   ├── main.css            base theme: navbar, buttons, forms, footer
│   │   ├── landing.css         landing page only
│   │   ├── problems.css        problem list/detail/create/edit pages
│   │   └── auth.css            login/register/profile pages
│   └── js/
│       └── main.js             navbar, mobile menu, tabs, scroll animations
│
└── templates/
    └── base.html               shared layout — nav, footer, flash messages
```

Each app keeps its own templates inside `app_name/templates/app_name/`,
which is the standard Django convention — it keeps things modular and
avoids name collisions as the project grows.

---

## What's on each page

| URL | Page |
|---|---|
| `/` | Landing — photo hero, stats, "why MindBridge", how it works preview, recent stories |
| `/problems/` | Browse & search every story, filter by category/status, sort |
| `/problems/new/` | Share a new problem |
| `/problems/<slug>/` | Full story + solutions + comments + voting |
| `/problems/<slug>/edit/` | Edit your own post |
| `/problems/category/<slug>/` | Stories filtered to one category |
| `/problems/mine/` | Your dashboard — your posts and your solutions |
| `/about/` | Why the site exists |
| `/how-it-works/` | Step-by-step guide + FAQ |
| `/contact/` | Contact info + crisis note |
| `/accounts/register/` | Create an account |
| `/accounts/login/` | Sign in |
| `/accounts/profile/<username>/` | Public profile |
| `/admin/` | Django admin |

---

## Notes on the code

- No JS frameworks, no CSS frameworks — plain Django templates, vanilla CSS
  with custom properties, and one small `main.js` file. Nothing to build or
  compile.
- All anonymity logic lives in the model methods (`display_author()`), so
  templates never need to branch on `is_anonymous` themselves.
- Search/filter/sort all happen in one view (`problem_list`) using
  Django's ORM — no extra search engine needed for this scale.
- Slugs auto-generate from titles and stay unique even if two people pick
  the same title.
- `seed_data` is idempotent — running it twice won't duplicate problems.

---

Built with Django + SQLite + vanilla CSS/JS.
