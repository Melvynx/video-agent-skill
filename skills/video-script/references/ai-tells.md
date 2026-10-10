# AI tells

What makes a text read as written by a chat model, and what a person would say instead. Judge each hit in its sentence: a list word used for its plain meaning ("navigate to the settings page") can stay.

## Words, English

| Tell | Say instead |
|---|---|
| delve, dive into, deep dive | look at, get into, or cut |
| leverage, utilize, harness | use |
| unlock, unleash | get, open, or cut |
| elevate, supercharge, take to the next level | improve, or say by how much |
| empower, enable | let, help |
| seamless, seamlessly | smooth, or cut |
| robust, comprehensive, holistic | solid, full, complete, or cut |
| cutting-edge, state-of-the-art, revolutionary | new, or name what is new |
| game-changer, game-changing | say what it changed |
| transformative, pivotal, paramount | big, key, or cut |
| crucial, vital, essential (over and over) | important, or show why it matters |
| meticulous, intricate, nuanced, multifaceted | careful, detailed, or show the detail |
| landscape, realm, tapestry, ecosystem | the field, the market, or name it |
| navigate (a problem), embark, journey | deal with, start, or say what happened |
| foster, cultivate | build, grow |
| testament to, underscores, showcases | shows |
| boasts | has |
| vibrant, bustling, dynamic | name the detail that makes it so |
| moreover, furthermore, additionally | and, also, or a new sentence |
| ultimately, in conclusion, in summary | cut |
| it's worth noting, it's important to note | cut, just say it |
| in today's fast-paced world, in the ever-evolving | cut |
| whether you're a beginner or a pro | say who it's for |
| buckle up, let's dive in, without further ado | cut |
| secret sauce, the magic | the reason, the trick, or name it |
| skyrocket, explode (growth) | the number |

## Words, French

| Tell | Say instead |
|---|---|
| plonger dans, plongeons dans | regarder, voir, or cut |
| explorer, naviguer (au sens figuré) | voir, gérer |
| véritable, un vrai game-changer | say what it changed |
| révolutionner, révolutionnaire | changer, or say how |
| incontournable, indispensable (partout) | utile, or show why |
| crucial, primordial, essentiel, fondamental | important, or show why |
| booster, décupler, propulser, sublimer | améliorer, or the number |
| optimiser, maximiser (sans chiffre) | améliorer, or the number |
| tirer parti de, exploiter, levier | utiliser, profiter de |
| permettre de (in every sentence) | the verb itself ("ça coupe" not "ça permet de couper") |
| la clé du succès, la clé c'est | le truc, ce qui marche, or say it |
| en effet, de plus, par ailleurs, en outre | et, aussi, or a new sentence |
| ainsi, en somme, en conclusion, en résumé | cut |
| il est important de noter, il convient de | cut, just say it |
| dans un monde où, à l'ère du numérique, dans le paysage actuel | cut |
| sans plus attendre, accrochez-vous | cut |
| vous l'aurez compris | cut |
| n'hésitez pas à | the verb ("commente" not "n'hésite pas à commenter") |
| que vous soyez débutant ou expert | say who it's for |

Other languages: the same two families. Formal linking words ("moreover", "en outre") nobody says out loud, and hype words that claim a result instead of showing it.

## Structure

### False contrast
"It's not X, it's Y." / "Ce n'est pas X, c'est Y." / "It's not just X, it's Y."
Fix: say Y. Keep one per text at most, and only when X is a belief the viewer really holds.

### Triads everywhere
"Fast, simple, and powerful." Three adjectives, three parallel clauses, three bullet points, again and again.
Fix: keep the one that matters, or give two, or give four with real content.

### Staccato fragments
"No fluff. No filler. Just results."
Fix: one real sentence that says what they get.

### The reveal question
"The result?" "The best part?" "The catch?" "Le résultat ?" "Le pire ?"
Fix: say the result. A real question to the viewer is fine; a question you answer in the next word is not.

### Preamble
"Here's the thing:", "Let me explain.", "Let's break it down.", "Here's why:", "Voici pourquoi :", "Laisse-moi t'expliquer."
Fix: start with the thing.

### Recap ending
"In short...", "Bottom line:", "So remember...", the hook said again in the last line.
Fix: end on the last useful line, then the ask.

### Engagement bait
"Save this for later", "Tag someone who needs this", "Agree?", "Comment below", "Like if...".
Fix: only the ask from voice.md, once. A comment ask names one exact word or question.

### Hedge stacks
"This can potentially help you", "may often lead to", "pourrait peut-être".
Fix: say it, or say the real condition ("if your videos are under 30 s").

### Both sides when the creator has a side
"While X has its benefits, Y also..."
Fix: their position, then the fair reason others disagree, once.

### Generic examples
"Imagine a business owner who...", "Company X", "a friend of mine" (invented).
Fix: a real example from voice.md or the conversation, else `{{a real example}}`.

### Flat rhythm
Every sentence 12 to 16 words, same shape, same start ("This...", "This...", "This...").
Fix: vary the length. Short line. Then a longer one that carries the detail. Start lines differently.

### Rhetorical question chains
"Want more views? Tired of posting into the void? Ready to grow?"
Fix: one question at most, then the answer.

## Typography

- **Em dash (U+2014)**: the strongest tell in English captions. Comma, period, colon or parentheses instead.
- **Emoji bullets** (🚀 ✅ 💡 👉 at the start of each line): plain lines, or one emoji where they would really use it.
- **Arrow bullets** (→ at the start of every line in a caption): plain lines.
- **Bold everywhere**: none, or one phrase.
- **Title Case In Captions**: sentence case.
- **Hashtag walls**: at most 5, at the end.
- **Colon headlines** ("Growth: The Untold Truth"): a plain sentence.

## Invisible characters

| Character | Bytes (UTF-8) | Keep? |
|---|---|---|
| zero-width space U+200B | e2 80 8b | remove |
| zero-width non-joiner U+200C | e2 80 8c | remove |
| zero-width joiner U+200D | e2 80 8d | remove, except inside an emoji |
| word joiner U+2060 | e2 81 a0 | remove |
| byte-order mark U+FEFF | ef bb bf | remove |
| soft hyphen U+00AD | c2 ad | remove |
| tag characters U+E0000 to U+E007F | f3 a0 80 xx, f3 a0 81 xx | remove (they can carry hidden text), except inside a flag emoji like Scotland's |
| no-break space U+00A0, narrow no-break space U+202F | c2 a0, e2 80 af | keep in French before `: ; ! ?` and inside `« »`, else a normal space |

They mostly come from copying text between apps and tools. Removing them keeps files clean and captions from breaking oddly; it says nothing about who wrote the text.
