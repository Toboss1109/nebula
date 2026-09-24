# Kurokami — монгол хэлтэй манхва / манга / манхуа уншигч

Astro + React + Tailwind дээр бүтсэн вэб сайт. Интерфейс бүрэн монгол хэл дээр, Supabase-ээр нэвтрэх систем (имэйл + нууц үг) болон хадгалсан / унших түүхийн синхрончлол бүхий.

## Ажиллуулах

```sh
npm install
cp .env.example .env     # дараа нь доорх Supabase тохиргоог бөглөнө
npm run dev              # http://localhost:4321
npm run build            # production build
```

Node.js 22 шаардлагатай (`package.json` дотор `engines` тохируулсан).

## Нэвтрэх систем (Supabase) тохируулах

Нэвтрэх хэсэг Supabase-ийн үнэгүй төлөвлөгөөн дээр ажиллана. `.env` хоосон бол сайт хэвийн ажиллах ч `/login` хуудсанд "тохируулагдаагүй" гэсэн анхааруулга гарна.

1. [supabase.com](https://supabase.com) дээр шинэ project үүсгэнэ.
2. **SQL Editor** дээр `supabase/schema.sql` файлын агуулгыг ажиллуулна (`user_data` хүснэгт + Row Level Security).
3. **Project Settings → API** хэсгээс `Project URL` болон `anon public` түлхүүрийг авч `.env`-д хийнэ:
   ```
   PUBLIC_SUPABASE_URL=https://xxxx.supabase.co
   PUBLIC_SUPABASE_ANON_KEY=eyJ...
   ```
4. **Authentication → URL Configuration** хэсэгт `Site URL`-ээ (жишээ: `https://your-site.vercel.app`) болон Redirect URLs-д `http://localhost:4321/**`, `https://your-site.vercel.app/**`-г нэмнэ (нууц үг сэргээх холбоос ажиллахад шаардлагатай).
5. Хөгжүүлэлтийн үед имэйл баталгаажуулалтыг түр унтраах бол **Authentication → Providers → Email → Confirm email**-ийг идэвхгүй болгоно.

### Vercel дээр
Project → Settings → Environment Variables хэсэгт дээрх 2 хувьсагчийг нэмээд дахин deploy хийнэ.

Node.js хувилбар `package.json`-ийн `engines` (22.x)-ээр тогтоогдоно. Vercel-ийн Project → Settings → General → Node.js Version нь 22.x байхад болно. `@astrojs/vercel` 8.x нь зөвхөн Node 20/22-ыг дэмждэг тул Node 24 бол runtime нь Node 18 болж унадаг.

## Нэвтрэх систем хэрхэн ажилладаг вэ

| Хуудас | Зориулалт |
|--------|-----------|
| `/login`, `/register` | Нэвтрэх, бүртгүүлэх |
| `/forgot-password`, `/reset-password` | Нууц үг сэргээх |

- Хадгалсан (`kurokami_bookmarks`), унших түүх (`kurokami_history`), уншсан бүлэг, сүүлд уншсан бүлгийг сайт өмнөх шигээ localStorage-д бичдэг. `src/utils/sync.ts` эдгээрийг чагнаж, нэвтэрсэн үед Supabase-ийн `user_data` хүснэгттэй синхрончилно.
- Нэвтрээгүй үед хуучин ажиллагаагаараа (зөвхөн хөтөч дээр) хэвээр байна.
- Анх нэвтрэхэд төхөөрөмж дээрх өгөгдөл бүртгэлийн өгөгдөлтэй нэгтгэгдэнэ. Дараа нь бүртгэл нь үнэний эх сурвалж болно (нэг төхөөрөмж дээр устгасан зүйл бусад дээр ч устна).
- "Гарах" дарахад өөрчлөлтийг илгээгээд, төхөөрөмжөөс локал өгөгдлийг цэвэрлэнэ.

## Бүтэц

```
src/pages/*.astro          маршрут бүр (SSR, SEO meta)
src/pages/_*.tsx           хуудас бүрийн React component
src/pages/_Auth.tsx        нэвтрэх / бүртгүүлэх / сэргээх форм
src/components/common/     Navbar, BottomNav, Footer, AccountButton
src/utils/api.ts           Shinigami API дуудлагууд
src/utils/supabase.ts      Supabase клиент
src/utils/sync.ts          localStorage ↔ Supabase синхрончлол
supabase/schema.sql        өгөгдлийн сангийн схем
```

## Анхаарах зүйл

Контентыг гуравдагч талын (Shinigami) албан бус API-аас авдаг. Сайтыг олон нийтэд нээх, сурталчилгаа оруулахаас өмнө контентын эрхийн асуудлыг тусад нь бодолцоорой.
