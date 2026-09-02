# LAN november — spillavstemning

En enkel side hvor kolleger kan stemme på hvilke spill dere skal spille på LAN-en, med søk mot Steam-biblioteket for å legge til flere spill.

## 1. Sett opp databasen (Supabase, gratis)

Stemmer og spill lagt til via søket lagres i en database, ikke i selve filen.

1. Gå til [supabase.com](https://supabase.com) → opprett gratis konto → **"New project"**
2. Åpne **SQL Editor** i menyen til venstre, lim inn koden under, og trykk **Run**:

```sql
create table votes (
  game_id text not null,
  voter_name text not null,
  created_at timestamptz default now(),
  primary key (game_id, voter_name)
);

create table custom_games (
  id bigint primary key,
  title text not null,
  price numeric,
  was numeric,
  description text,
  extra_img text,
  added_by text,
  created_at timestamptz default now()
);

alter table votes enable row level security;
alter table custom_games enable row level security;

create policy "public read votes" on votes for select using (true);
create policy "public write votes" on votes for insert with check (true);
create policy "public delete votes" on votes for delete using (true);

create policy "public read games" on custom_games for select using (true);
create policy "public write games" on custom_games for insert with check (true);
```

3. Gå til **Settings → API**. Kopier **Project URL** og **anon public**-nøkkelen.
4. Åpne `index.html` i en teksteditor, finn disse to linjene nær toppen av `<script>`-delen, og lim inn dine verdier:

```js
const SUPABASE_URL = "YOUR_SUPABASE_URL";
const SUPABASE_ANON_KEY = "YOUR_SUPABASE_ANON_KEY";
```

5. Lagre filen.

> Anon-nøkkelen er ment å ligge åpent i klientkoden — det er sånn Supabase er designet. Policyene over gir alle med lenken lov til å lese, stemme og legge til spill. Fint for et internt LAN-verktøy, ikke bruk denne databasen til noe sensitivt.

## 2. Last opp til GitHub

1. Last opp **alle filene i denne mappen** (`index.html`, `.nojekyll`, `README.md`) til roten av GitHub-repoet ditt — dra dem inn under **Add file → Upload files**
2. Sørg for at repoet er **public** (Settings → General → Danger Zone → Change visibility), ellers krever GitHub Pages betalt plan
3. Gå til **Settings → Pages**
4. Under **Build and deployment → Source**, velg **"Deploy from a branch"**
5. Velg branch **main** og mappe **/ (root)**, trykk **Save**
6. Vent 1–2 minutter — siden blir tilgjengelig på `https://<brukernavn>.github.io/<repo-navn>/`

## Filstruktur

```
.
├── index.html     ← selve nettsiden (må hete akkurat dette)
├── .nojekyll      ← forteller GitHub Pages å ikke Jekyll-prosessere filene
└── README.md      ← denne filen
```
