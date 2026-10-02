# ChixyCollect

Offline-capable field survey PWA. Survey drafts are stored on the device; queued surveys upload to Supabase when a signed-in user is online.

## Configure Supabase

1. Create a Supabase project.
2. Run `supabase/migrations/20261001102325_create_mutare_asset_survey.sql` in the Supabase SQL Editor. The migration does not grant profile-update access to ordinary users; assign elevated roles only from the SQL Editor.
3. In **Authentication > Sign In / Providers > Email**, turn off **Confirm email** for the demo. This avoids confirmation emails; new users are signed in immediately after registration.
4. Users can register in the app with their name, email, and password, then sign in with those credentials later. The profile trigger saves their name.
5. Configure the Supabase Site URL and allowed redirect URLs for local and deployed app addresses. For real users, configure a trusted custom SMTP provider for reliable email delivery.
6. Copy `.env.example` to `.env.local` for local development and set `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` from the Supabase project's API settings. Never expose a service-role key in this app.

To promote a trusted account, run this in the Supabase SQL Editor, replacing the email:

```sql
UPDATE public.profiles
SET role = 'admin'
WHERE id = (SELECT id FROM auth.users WHERE email = 'admin@example.com');
```

## Deploy to Vercel

1. Push the project to a GitHub repository and import it into Vercel.
2. Set the Vercel environment variables `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` for the deployment environment.
3. Use `npm run build` as the build command and `dist` as the output directory, then deploy.
4. In Supabase **Authentication > URL Configuration**, set the Site URL to the deployed HTTPS URL and add that URL to the allowed redirect URLs.
5. Sign in with a real account, queue a test survey, select **Upload to server**, and verify it appears in the Supabase `survey_features` table.

## Install on phones

Open the deployed HTTPS URL on each phone and sign in. On Android, use Chrome's menu and select **Install app** or **Add to Home screen**. On iPhone, open the URL in Safari, select **Share**, then **Add to Home Screen**.

Surveys saved as drafts or waiting in the queue remain in that phone's browser storage until uploaded. Upload while online to save survey records in `survey_features` and photos in the private `survey-photos` bucket. Apply `supabase/migrations/20261002120000_allow_photo_upserts.sql` in Supabase to allow safe photo upload retries.