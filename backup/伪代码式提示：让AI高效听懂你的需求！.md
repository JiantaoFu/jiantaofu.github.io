# 什么是伪代码式提示？

不是写代码！而是用逻辑清晰、结构化的方式告诉AI你想要什么，让AI更懂你，少踩坑，多省心！

# 为什么要用伪代码式提示？

1️⃣ 逻辑清晰：明确每一步要干啥，AI按流程来，结果不乱套！

2️⃣ 内容完整：不会忘东忘西，细节都能照顾到。

3️⃣ 输出一致：每次结果都整整齐齐，再也不用担心AI“自由发挥”！

4️⃣ 易读好懂：简单直白，自己看得明白，别人也能快速接手修改。

5️⃣ 维护超方便：想改需求？动一小块就行，不用重写！

# 任务案例：生成一份旅游行程

## 需求背景
假设你希望AI生成一份去夏威夷的5天旅行计划，包括每天的活动安排、推荐餐厅和注意事项。你可以用自然语言或伪代码两种方式提出需求。

### 1. 自然语言提示

"帮我生成一个夏威夷的5天旅游计划，包括每天的活动、餐厅推荐和注意事项。尽量详细一些，适合家庭旅行，包含文化景点、自然风光和轻松的活动安排。"

可能的问题

- 逻辑不清晰: AI可能随机列出景点，但没有明确每天安排的时间顺序和逻辑结构（如早上-中午-晚上）。
- 内容不完整: AI可能遗漏某些需求，如没有推荐餐厅或忽略注意事项。
- 输出不一致: 每天的描述可能详略不一，有的天活动很多，有的天很少。
- 维护困难: 如果需要调整（如强调某类活动），需要重新设计整个提示。

### 2. 伪代码提示

```
INPUT:

  - Destination = "Hawaii"

  - Duration = 5 days

  - Travel Style = "Family-friendly, mix of culture, nature, and relaxation"

TASK:

  1. Generate a {Duration} itinerary with the following structure:

     - For each day:

       a. Morning: Activity or excursion (e.g., sightseeing, nature walk).

       b. Afternoon: Light activity or cultural experience.

       c. Evening: Dinner recommendation + leisure activity.

     - Include at least one nature-focused day (e.g., beaches, hiking).

     - Include one day with a cultural focus (e.g., museums, local traditions).

     - Ensure activities are family-friendly and suitable for all ages.

  2. For each day, provide:

     - A short description of the activities.

     - A recommended restaurant or food experience.

     - 1-2 travel tips or precautions (e.g., weather, booking requirements).

  3. Ensure balance across the days (not too rushed or repetitive).

OUTPUT:

  - A structured and cohesive {Duration}-day itinerary.
```

## 对比和优势展示

- 逻辑清晰: 伪代码提示明确每一天的逻辑顺序：早上-中午-晚上安排分明，避免内容随机堆叠。自然语言可能不够具体，逻辑易混乱。
- 内容完整: 伪代码通过定义模块（活动、餐厅、注意事项），确保每个需求都被覆盖。而自然语言提示可能遗漏某些细节。
- 一致性强: 每天的内容按相同结构生成（活动+餐厅+注意事项），避免自然语言提示中可能出现的不均衡问题。
- 易读易理解: 伪代码结构化表达更直观，尤其适合团队协作或长任务。自然语言可能需要反复阅读才能抓住重点。
- 维护与扩展方便: 若需要调整为7天行程，伪代码仅需修改 Duration = 7 days，自然语言提示则可能需要重写提示内容。
- 提高人机协作效率: 伪代码清晰地定义#了任务，AI更容易一次性输出符合预期的结果，减少试错时间。自然语言需要反复调整描述，效率较低。

## 输出对比

### 自然语言提示生成示例

```
Day 1: Visit Waikiki Beach and enjoy lunch at Duke’s Waikiki. Spend the evening exploring the nearby shops.

Day 2: Take a nature hike at Diamond Head. Grab lunch at a local cafe. Visit a cultural museum in the afternoon.

(后续内容可能缺乏逻辑顺序或不够详细)
```

### 伪代码提示生成示例

```
Day 1

Morning
: Arrival and check-in at Waikiki. Take a stroll along Waikiki Beach.
Afternoon
: Visit Diamond Head for a light hike. Enjoy a scenic view of Honolulu.
Evening
: Dinner at Duke’s Waikiki (try their famous hula pie). Leisure: Explore Kalakaua Avenue shops.
Tips
: Bring sunscreen; reserve dinner early to avoid wait times.
Day 2

Morning
: Visit the Iolani Palace for a cultural experience.
Afternoon
: Explore the Honolulu Museum of Art. Have lunch at Helena’s Hawaiian Food.
Evening
: Relax on Magic Island at Ala Moana Beach Park.
Tips
: Book Iolani Palace tickets in advance; carry a water bottle.
(内容继续，结构一致，细节丰富)
```

# 总结

伪代码提示的结构化优势让任务更清晰、内容更全面、输出更一致，同时便于维护和扩展。在需要高质量、复杂任务的场景下，它显然比自然语言提示更高效。