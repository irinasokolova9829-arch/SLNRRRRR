# План сайта Solanero

## Цель и исходные материалы
Пользователь попросил встроить присланный скриншот отдельной основной вкладкой сайта Solanero. Скриншот задаёт Private Route screen: верхнее позиционирование, большая карта SOL → XMR → SOL, получающая Solana destination, расчётную панель и правую колонку с четырьмя шагами и деталями маршрута. Сохранить фирменную тёмную Solanero-стилистику и адаптировать lime-акценты скриншота. Проектные первоисточники — [публичный профиль X](https://x.com/solanero_mix) и [статья Solanero](https://x.com/solanero_mix/article/2109688359211642988).

## Реализация
Приложение остаётся статическим frontend на Vite + React + TypeScript, без базы данных и backend. Использовать два page route — `/` и `/route`; расширить `public/manus-routes.json` для обоих маршрутов. Существующая главная `/` и market charts сохраняются; Private Route становится первой заметной ссылкой общей навигации и главным CTA главной страницы. На `/route` реализовать пользовательскую настройку SOL → XMR → SOL с редактируемыми amount и Solana destination, этапами маршрута и деталями, стилистически соответствующими скриншоту.

Форма валидирует положительное количество SOL и базовую форму Solana public address в клиенте. При opt-in Phantom connect текущий public address может быть скопирован в поле назначения только отдельным явным нажатием «Use connected wallet». Поля существуют только во временной памяти вкладки: не читается wallet balance, не вызываются RPC/quote APIs, не передаются и не журналируются amount/address, ничего не сохраняется в localStorage, server или database.

Ключевая недостающая возможность — проверенный route provider для SOL ↔ XMR и API с фактическими котировками. До появления такого провайдера любые estimate-return, rate, fee и delivery-time поля остаются «—»; при отправке формы UI показывает отсутствие live quote provider и сообщает, что транзакция не отправлена. Не выдумывать число SOL на выходе, курс, комиссию, маршрутную доступность или срок доставки. Сайт не отправляет и не подписывает сообщения и транзакции, не запрашивает approvals, не принимает SOL/XMR и не выполняет swap/deposit/withdraw. Не подключать баланс, договоры или RPC без явно предоставленной проверенной интеграции.

## Структура проекта
- `index.html` — начальная HTML-страница и SEO/Open Graph metadata.
- `src/main.tsx` — React entrypoint.
- `src/App.tsx` — selects homepage `/` or `PrivateRoutePage` at `/route`.
- `src/components/SiteHeader.tsx` — common announcement/header/navigation, prominent Private Route tab, cross-page anchors and optional Phantom control.
- `src/components/PrivateRoutePage.tsx` — main route configurator, form validation, explicit Phantom-address prefill, route-only informational status.
- `src/components/private-route.css` — responsive route form, quote preview, route steps/details and mobile layout.
- `src/components/ProjectMark.tsx` — reusable Solanero brand sign.
- `src/components/PhantomWalletButton.tsx` — manual Phantom connection/disconnection and public address display; optionally reports the public address only to the mounted route page for an explicitly initiated prefill.
- `src/components/MarketCharts.tsx` and `src/lib/marketData.ts` — public SOL/BTC/XMR USD chart series, attribution, cache, rate limiting and freshness states.
- `src/components/market.css` — responsive market charts and explicitly unavailable SOL/XMR liquidity pool.
- `src/styles.css` — global type, color tokens, shared header and reduced-motion behavior.
- `public/manus-routes.json` — route manifest for `/` and `/route`.
- `docs/integrations.md` — external-source facts and implementation limits.
- `plan.md` and `TODO.md` — current plan and acceptance outcomes.
- `/manus-storage/` — durable project-stored artwork from Solanero X profile.
- `app.config.ts` — existing absolute HTTPS logo metadata.

No packages, provider keys, database, or server features are added for this UI task. Existing configuration stays on port 3000 with production build `npm ci && npm run build` into `dist/`; public publication is not part of this request.

## Визуальная спецификация Private Route
- **Design movement:** neo-cyberpunk fintech product interface, with soft technical precision rather than a speculative crypto terminal; explicitly adapt the attached user screenshot.
- **Core principles:** prioritize route setup in navigation; keep send/route/receive steps visibly connected; show values only when sourced; keep unconfigured execution functions inert and explain their availability plainly.
- **Color philosophy:** retain near-black navy `#070911`, raised graphite panels, Solanero cyan `#58E1FF` and the official orange accent; introduce screenshot lime `#B7FF69` specifically for route emphasis and its primary local action, not as an indication of a live transaction.
- **Layout paradigm:** compact, centered product header; large centered route headline; asymmetric form-first two-column body (editable exchange card on the left and steps/details rail on the right); collapse the side rail below the form on narrower screens.
- **Signature elements:** rounded dark route/quote surfaces; stacked amount and receive token fields linked by small downward route markers; lime step ordinals paired with quiet cyan/orange asset signatures.
- **Interaction philosophy:** user chooses when to connect Phantom and when to prefill its public destination address; validation is inline; preview submission is local-only and returns an honest unavailable-quote message until a provider exists.
- **Animation:** short focus and status transitions only, no fake loading countdown or quote animation; honor `prefers-reduced-motion`.
- **Typography system:** reuse existing Space Grotesk headings and Inter controls/body; use DM Mono sparingly for route labels, digits and timestamps; keep fine print readable.
- **Brand essence:** a clear Solanero home for configured SOL → XMR → SOL private routes, market context and public-address-only wallet access; personality: composed, direct, technically credible.
- **Brand voice:** short, action-led copy without implying a completed swap. Examples: “Your route. Your privacy.” and “Review every step before a provider quote is available.”
- **Wordmark & Logo:** use the existing custom M medallion and Solana bars with the hand-set Solanero wordmark.
- **Signature brand color:** retain Solanero cyan `#58E1FF`; use lime `#B7FF69` as a secondary route-specific accent drawn from the supplied reference.
