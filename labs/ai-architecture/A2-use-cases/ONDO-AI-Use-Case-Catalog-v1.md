# ONDO-AI-Use-Case-Catalog-v1.md

### Легенда по допущениям

* **Ставка FTE:** 25$/час (all-in cost, включая налоги и рабочее место).
* **Рабочие часы:** 220 рабочих дней в году.
* **TCO Template:** Year 0 (CAPEX) + 3 * (Yearly OPEX).
* **Net 3-yr ROI:** Формула (Суммарный Benefit за 3 года - 3-Year TCO).
* **Hardware Constraint:** 1x RTX 3090 24GB VRAM. OS overhead 1.5GB учтен (доступно ~22.5GB). Никаких ресурсоемких параллельных задач на одной карте.
* **Security Framework:** OWASP LLM Top 10:2025.

---

## 1. Billing - Intelligent AP Invoice Processing

1. **Use Case Name:** Intelligent AP Invoice Processing
2. **Domain:** billing / finance (SAP FI)
3. **Problem Description:** Бухгалтерия вручную переносит данные из PDF/сканов счетов контрагентов в SAP FI/AP, тратя сотни часов и допуская опечатки.
4. **AI/ML Approach:** Multimodal extraction (qwen3-vl:8b) -> JSON parsing.
5. **Expected Value:** High - прямой финансовый эффект от сокращения рутины и штрафов за просрочку.
6. **Complexity:** Medium - qwen3-vl легкая (6.1GB), но интеграция с SAP требует четкого маппинга полей.
7. **Priority:** P2 - Long-term ROI, Break-even 36 месяцев (не квалифицируется как Quick Win).
8. **Threat Analysis:** - LLM01 Prompt Injection - Medium (злоумышленник вшивает скрытый текст в PDF для подмены IBAN).
* LLM02 Sensitive Information Disclosure - High (финансовые данные).
* LLM05 Improper Output Handling - High (слепая отправка JSON в SAP без валидации).


9. **TCO Estimate:** Year 0 (30K data/integration + 25K dev) + OPEX (20K/yr) = **115K** (3-year).
10. **ROI Projection & Net 3-yr:** Экономия 1320 ч/год (33K$) + снижение штрафов (5K$) = 38K$/год. За 3 года = 114K$. **Net 3-yr ROI: -$1K.**
*Strategic Justification:* Проект закладывает архитектурный фундамент (Computer Vision + SAP BAPI интеграция), который будет переиспользован для других форматов документов.
11. **SAP/ERP Integration:** SAP FI/AP. Создание предварительного документа через BAPI_ACC_DOCUMENT_POST (read/write).
12. **Compliance:** Украинские стандарты бухучета. GDPR обязателен (счета содержат PII контактных лиц, ФИО и ИНН индивидуальных предпринимателей/ФОП).
13. **Build vs Buy:** Hybrid (локальная qwen3-vl + своя интеграция для защиты данных).

## 2. Billing - Tariff & Benefits RAG Assistant

1. **Use Case Name:** Tariff & Benefits Assistant for Call Center
2. **Domain:** customer service / billing
3. **Problem Description:** Операторы колл-центра ищут актуальные тарифы и правила льгот в разрозненных PDF, увеличивая AHT (Average Handle Time).
4. **AI/ML Approach:** RAG (qwen3-embedding + qwen3:14b).
5. **Expected Value:** Medium-High - снижение AHT и количества ошибок при консультациях абонентов.
6. **Complexity:** Medium - классический RAG, умеренные требования к железу.
7. **Priority:** P1.
8. **Threat Analysis:** - LLM02 Sensitive Information Disclosure - Medium (если в индекс попадут персональные субсидии).
* LLM08 Vector & Embedding Weaknesses - Medium (несанкционированный доступ к Milvus/Chroma).
* LLM09 Misinformation - High (галлюцинация в ответе о тарифах приведет к жалобам в НКРЕКП).


9. **TCO Estimate:** Year 0 (40K) + OPEX (18K/yr) = **94K** (3-year).
10. **ROI Projection & Net 3-yr:** Экономия времени 18K$/год. Baseline для перерасчетов: 80 эскалаций/мес * 1 час rework * 25$ = 24K$/год. Суммарный бенефит = 42K$/год. За 3 года = 126K$. **Net 3-yr ROI: +$32K.**
11. **SAP/ERP Integration:** SAP IS-U. Read-only доступ к профилю клиента через REST для контекста.
12. **Compliance:** GDPR (защита данных абонента).
13. **Build vs Buy:** Build (полный контроль над векторной БД on-prem).

## 3. Operations - Outage Incident Summarizer

1. **Use Case Name:** Outage Incident Text Summarizer
2. **Domain:** operations / SCADA
3. **Problem Description:** При крупных авариях диспетчеры тонут в потоке текстовых заявок и отчетов бригад. Трудно составить сводку для руководства.
4. **AI/ML Approach:** Text summarization & Entity extraction (qwen3:14b).
5. **Expected Value:** Medium - ускорение отчетности в критические моменты.
6. **Complexity:** Low-Medium - работает только с текстом.
7. **Priority:** P2.
8. **Threat Analysis:** - LLM02 Sensitive Info Disclosure - High (топология сети в отчетах).
* LLM07 System Prompt Leakage - Low (внутренний тул).
* LLM09 Misinformation - Critical (пропуск критической детали из отчета бригады).


9. **TCO Estimate:** Year 0 (35K) + OPEX (15K/yr) = **80K** (3-year).
10. **ROI Projection & Net 3-yr:** Экономия 500 ч/год диспетчеров (12.5K$) + снижение SLA penalties = 7.5K$/год. За 3 года = 60K$. **Net 3-yr ROI: -$20K.**
*Strategic Justification:* Risk avoidance. Предотвращение задержек в отчетности перед регулятором во время Major Incidents. Штраф за несвоевременное информирование кратно превышает дефицит в 20K$.
11. **SAP/ERP Integration:** SAP PM (Plant Maintenance). Чтение статусов Work Orders.
12. **Compliance:** NIS2 (защита информации об инцидентах).
13. **Build vs Buy:** Build (100% локально из-за грифа "Secret" на данных сети).

## 4. SCADA/OT - Legacy Log Protocol Parser

1. **Use Case Name:** Legacy OT Protocol Parsing to JSON
2. **Domain:** SCADA/OT / IT
3. **Problem Description:** Старое оборудование выдает неструктурированные сырые дампы, которые SIEM системы ONDO не могут распарсить.
4. **AI/ML Approach:** Few-shot Prompting / Data Extraction (qwen3-coder:30b).
5. **Expected Value:** High - подключение слепых зон ОТ-инфраструктуры к мониторингу.
6. **Complexity:** Medium-High - требует высокой точности (T=0.0) и использования 30B модели.
7. **Priority:** P2.
8. **Threat Analysis:** - LLM01 Prompt Injection - Medium (вредоносный лог, ломающий парсер).
* LLM02 Sensitive Info Disclosure - Critical (IP-адреса и конфигурации ПЛК).
* LLM10 Unbounded Consumption - High (поток логов может вызвать OOM на RTX 3090).


9. **TCO Estimate:** Year 0 (45K) + OPEX (12K/yr) = **81K** (3-year).
10. **ROI Projection & Net 3-yr:** Baseline: создание 1 кастомного Regex-парсера = 60 часов. 2 парсера/мес * 60ч * 25$ = 36K$/год экономии. За 3 года = 108K$. **Net 3-yr ROI: +$27K.**
11. **SAP/ERP Integration:** N/A. Интеграция с локальным SIEM (Wazuh/Splunk).
12. **Compliance:** NIS2. Закон Украины "Про критичну інфраструктуру" (2021) - обязательное обеспечение мониторинга систем управления техпроцессами.
13. **Build vs Buy:** Build (только локально, Air-gapped).

## 5. SCADA/OT - Root Cause Analysis (RCA) Drafter

1. **Use Case Name:** OT Incident RCA Drafter
2. **Domain:** SCADA/OT / operations
3. **Problem Description:** Составление акта расследования аварии (RCA) занимает у инженеров до 8-10 часов сбора данных из разных систем.
4. **AI/ML Approach:** Agentic workflow + Generation (deepseek-r1:14b для reasoning).
5. **Expected Value:** High - стандартизация отчетов, ускорение закрытия инцидентов.
6. **Complexity:** High - требует цепочки вызовов к Zabbix/SCADA логам.
7. **Priority:** P3 (из-за сложности).
8. **Threat Analysis:** - LLM06 Excessive Agency - High (агент должен иметь только read-only права к логам).
* LLM09 Misinformation - Critical (ложный вывод о причине аварии).
* LLM02 Sensitive Info Disclosure - High.


9. **TCO Estimate:** Year 0 (65K) + OPEX (20K/yr) = **125K** (3-year).
10. **ROI Projection & Net 3-yr:** 200 инцидентов/год * 6 часов экономии = 1200 ч/год (30K$). За 3 года = 90K$. **Net 3-yr ROI: -$35K.**
*Strategic Justification:* Compliance necessity. Глубокий анализ причин аварий (deepseek-r1) снижает вероятность системных отказов оборудования в условиях военного времени.
11. **SAP/ERP Integration:** SAP PM. Автоматическое прикрепление черновика RCA к Work Order.
12. **Compliance:** Отраслевые требования к расследованию технологических нарушений.
13. **Build vs Buy:** Build (локальный Reasoning).

## 6. Customer Service - Self-Service Chatbot (Public)

1. **Use Case Name:** Customer Self-Service Chatbot
2. **Domain:** customer service / billing
3. **Problem Description:** Высокая нагрузка на колл-центр по типовым вопросам баланса и отключений.
4. **AI/ML Approach:** RAG + Chatbot UI.
5. **Expected Value:** High - прямое снижение OPEX колл-центра.
6. **Complexity:** High - интеграция облачного SaaS с локальным on-premise контуром.
7. **Priority:** P3.
8. **Threat Analysis:** - LLM01 Prompt Injection - High (публичный доступ, джейлбрейки).
* LLM02 Sensitive Info Disclosure - Critical (показ чужого баланса).
* LLM10 Unbounded Consumption - Critical (DoS атака на API Gateway).


9. **TCO Estimate:** Year 0 (70K) + OPEX (30K/yr) = **160K** (3-year).
10. **ROI Projection & Net 3-yr:** 30% снижение звонков = экономия 30K$/год. За 3 года = 90K$. **Net 3-yr ROI: -$70K.**
*Strategic Justification:* CX Strategy. Перевод пользователей в digital-каналы и снижение нагрузки на L1 поддержку при массовых отключениях.
11. **SAP/ERP Integration:** SAP IS-U / SAP CRM. Строго через API Gateway с авторизацией абонента.
12. **Compliance:** GDPR.
13. **Build vs Buy:** Hybrid. Buy: Внешний SaaS-диалоговый движок (в облаке, принимает DoS риски). Build: Локальная Ollama (RAG) в закрытом контуре. Ollama НЕ выставляется в DMZ.

## 7. HR/Internal - Internal Policy Assistant

1. **Use Case Name:** Internal Policy & Procedure Assistant
2. **Domain:** HR / internal
3. **Problem Description:** Поиск ответов по отпускам, охране труда и командировкам занимает время сотрудников.
4. **AI/ML Approach:** RAG (qwen3-embedding + qwen3:14b).
5. **Expected Value:** Low-Medium - удобство, но не влияет на core-бизнес.
6. **Complexity:** Low - идеальный Sandbox проект.
7. **Priority:** P2 (как тренировочный полигон).
8. **Threat Analysis:** - LLM01 Prompt Injection - Medium (внутренний нарушитель).
* LLM08 Vector & Embedding Weaknesses - Medium.
* LLM09 Misinformation - Medium (ошибка в процедуре отпуска не критична).


9. **TCO Estimate:** Year 0 (20K) + OPEX (9K/yr) = **47K** (3-year).
10. **ROI Projection & Net 3-yr:** Экономия времени 400 сотрудников = 20K$/год. За 3 года = 60K$. **Net 3-yr ROI: +$13K.**
11. **SAP/ERP Integration:** N/A. (Corporate Confluence - A: допущение).
12. **Compliance:** GDPR (исключить PII из индекса).
13. **Build vs Buy:** Build (локальный RAG).

## 8. Legal/Compliance - Regulatory Analyzer (НКРЕКП)

1. **Use Case Name:** Regulatory Compliance Analyzer (НКРЕКП)
2. **Domain:** legal / compliance
3. **Problem Description:** Юристы вручную сверяют новые правила НКРЕКП с регламентами ONDO.
4. **AI/ML Approach:** RAG + Document Comparison (deepseek-r1:14b).
5. **Expected Value:** High - предотвращение многомиллионных штрафов регулятора.
6. **Complexity:** Medium-High - требует сложного логического вывода (Reasoning).
7. **Priority:** P2.
8. **Threat Analysis:** - LLM02 Sensitive Info Disclosure - Medium.
* LLM08 Vector & Embedding Weaknesses - Medium.
* LLM09 Misinformation - Critical (галлюцинация в трактовке закона).


9. **TCO Estimate:** Year 0 (40K) + OPEX (15K/yr) = **85K** (3-year).
10. **ROI Projection & Net 3-yr:** Экономия 600 ч/год юристов (15K$) + предотвращение штрафов (50K$/год). За 3 года = 195K$. **Net 3-yr ROI: +$110K.**
11. **SAP/ERP Integration:** N/A (работа с документами PDF/Word).
12. **Compliance:** Внутренние политики ONDO.
13. **Build vs Buy:** Build (документы содержат конфиденциальные данные компании).

## 9. IT/SAP - Engineering RAG (L2/L3 & SAP Notes)

1. **Use Case Name:** SAP & IT Engineering Knowledge Assistant
2. **Domain:** IT / SAP
3. **Problem Description:** Инженеры тратят часы на поиск решений в SAP Notes и дампах конфигураций при инцидентах.
4. **AI/ML Approach:** RAG (qwen3-embedding + qwen3:14b).
5. **Expected Value:** High - прямое влияние на MTTR критических систем.
6. **Complexity:** Medium.
7. **Priority:** P1.
8. **Threat Analysis:** - LLM02 Sensitive Info Disclosure - Critical (утечка топологии, паролей Basis администраторов).
* LLM07 System Prompt Leakage - Medium.
* LLM08 Vector & Embedding Weaknesses - High.


9. **TCO Estimate:** Year 0 (45K) + OPEX (20K/yr) = **105K** (3-year).
10. **ROI Projection & Net 3-yr:** Снижение MTTR экономит 1000 ч/год (25K$) + снижение даунтайма SAP (20K$/год). За 3 года = 135K$. **Net 3-yr ROI: +$30K.**
11. **SAP/ERP Integration:** Интеграция с SAP Solution Manager (чтение документации).
12. **Compliance:** Строгий RBAC (Role-Based Access Control) обязателен, так как SAP Notes могут содержать конфигурации production-систем.
13. **Build vs Buy:** Build.

## 10. IT/Development - ABAP/Custom Dev Code Reviewer

1. **Use Case Name:** Code Review Assistant for SAP ABAP
2. **Domain:** IT / development
3. **Problem Description:** Junior-код содержит уязвимости и неоптимальные SQL-запросы к базе SAP. Дефицит Senior разработчиков.
4. **AI/ML Approach:** Code Completion & Review (qwen3-coder:30b / deepseek-coder-v2:16b).
5. **Expected Value:** High - повышение качества кода.
6. **Complexity:** Medium - плагины к IDE (Continue.dev) + локальная Ollama.
7. **Priority:** P1 (быстро разворачивается, нулевой CAPEX).
8. **Threat Analysis:** - LLM02 Sensitive Info Disclosure - High (хардкод-секреты в коде).
* LLM03 Supply Chain Vulnerabilities - High (риск использования уязвимого GGUF файла).
* LLM05 Improper Output Handling - High (слепое копирование кода в production).


9. **TCO Estimate:** Year 0 (10K - настройка Continue.dev) + OPEX (5K/yr) = **25K** (3-year).
10. **ROI Projection & Net 3-yr:** Ускорение разработки на 15% (3 разработчика) = 22K$/год. За 3 года = 66K$. **Net 3-yr ROI: +$41K.**
11. **SAP/ERP Integration:** N/A (работа в IDE разработчика, деплой через стандартный транспортный запрос).
12. **Compliance:** Запрет использования Antigravity/Cloud для ревью кода (правило ADL-04).
13. **Build vs Buy:** Build (строго локально).

## 11. Security - GenAI Data Leakage Monitor (DLP)

1. **Use Case Name:** GenAI Data Leakage Monitoring
2. **Domain:** IT / security
3. **Problem Description:** Риск утечки PII, SCADA-конфигов и SAP-секретов через неконтролируемое использование ChatGPT/Claude сотрудниками.
4. **AI/ML Approach:** Classification / Named Entity Recognition (NER).
5. **Expected Value:** High - защита от регуляторных рисков.
6. **Complexity:** Medium-High - интеграция с корпоративным прокси/firewall.
7. **Priority:** P1 (фундамент безопасности для всех AI-инициатив).
8. **Threat Analysis:** - LLM02 Sensitive Info Disclosure - Critical.
* LLM04 Data & Model Poisoning - Medium.
* LLM10 Unbounded Consumption - Medium (перегрузка классификатора заблокирует легитимный трафик).


9. **TCO Estimate:** Year 0 (45K) + OPEX (22K/yr) = **111K** (3-year).
10. **ROI Projection & Net 3-yr:** Штраф за утечку PII/SCADA = 200K$+. **Net 3-yr ROI: Positive (через Risk Avoidance).**
11. **SAP/ERP Integration:** Защита данных, выгружаемых из SAP.
12. **Compliance:** GDPR, NIS2.
13. **Build vs Buy:** Hybrid. Buy (базовый DLP шлюз) + Build (кастомные NER правила для локальной LLM).

## 12. Legal/Procurement - Contracts Risk Scorer

1. **Use Case Name:** Procurement Contracts Risk Scorer
2. **Domain:** legal / procurement
3. **Problem Description:** Согласование договоров занимает недели. Риск пропустить скрытые штрафные санкции в PDF на 50 страниц.
4. **AI/ML Approach:** Information Extraction & Semantic Matching (qwen3:14b).
5. **Expected Value:** High - ускорение закупок оборудования.
6. **Complexity:** Medium - анализ длинных текстов (внимание к лимиту KV-Cache).
7. **Priority:** P2.
8. **Threat Analysis:** - LLM01 Prompt Injection - High (подрядчик вшил скрытый prompt в PDF для занижения risk score).
* LLM02 Sensitive Info Disclosure - High (коммерческая тайна ONDO).
* LLM09 Misinformation - High (модель "не увидела" скрытый штраф).


9. **TCO Estimate:** Year 0 (35K) + OPEX (15K/yr) = **80K** (3-year).
10. **ROI Projection & Net 3-yr:** Экономия 400 ч/год юристов (10K$) + предотвращение невыгодных контрактов (25K$/год). За 3 года = 105K$. **Net 3-yr ROI: +$25K.**
11. **SAP/ERP Integration:** SAP MM (Materials Management). Чтение черновика контракта.
12. **Compliance:** Внутренние тендерные политики.
13. **Build vs Buy:** Build (локально, контракты = коммерческая тайна).

## 13. Operations - Dispatcher Post-Call Notes Classifier

1. **Use Case Name:** Dispatcher Post-Call Notes Classifier
2. **Domain:** operations / customer service
3. **Problem Description:** Диспетчеры тратят время на ручную классификацию и заполнение карточек инцидентов после разговора с абонентом по аварии.
4. **AI/ML Approach:** Text Classification & Summarization (qwen3:14b). **Hardware Constraint:** Real-time ASR (распознавание речи) - вне скоупа из-за VRAM constraints на RTX 3090. Scope: post-call text processing only.
5. **Expected Value:** High - стандартизация данных об авариях, ускорение постобработки звонка.
6. **Complexity:** Low - работа с короткими текстовыми заметками.
7. **Priority:** P1 (Quick Win, не требует тяжелых интеграций).
8. **Threat Analysis:** - LLM01 Prompt Injection - Low (данные генерируются внутренними системами).
* LLM02 Sensitive Info Disclosure - High (адреса, контакты абонентов).
* LLM09 Misinformation - High (неверная классификация типа аварии).


9. **TCO Estimate:** Year 0 (20K) + OPEX (10K/yr) = **50K** (3-year).
10. **ROI Projection & Net 3-yr:** Экономия 1 мин на постобработке каждого звонка. 60 звонков/день * 10 диспетчеров = 600 мин/день (10 ч/день). 10ч * 220 дней * 25$ = 55K$/год. За 3 года = 165K$. **Net 3-yr ROI: +$115K.**
11. **SAP/ERP Integration:** SAP CS (Customer Service) - обновление статуса заявки через API.
12. **Compliance:** GDPR (обработка данных абонента).
13. **Build vs Buy:** Build (локальная обработка коротких текстов на qwen3:14b).

---