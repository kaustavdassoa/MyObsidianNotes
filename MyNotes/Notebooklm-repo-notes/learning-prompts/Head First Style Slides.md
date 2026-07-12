Here is a prompt you can copy and paste directly into NotebookLM to generate the presentation outline. This prompt instructs the AI to stick _only_ to your saved notes and to adopt the specific, quirky "Head First" style.

**Copy and paste this into NotebookLM:**

> **System Instructions & Goal:**
> 
> You are an expert instructional designer and technical writer who specializes in the "Head First" book series style (specifically _Head First Design Patterns_). I need you to create a detailed, 15-slide presentation outline on the topic of "Harness Engineering."
> 
> **Strict Data Constraint:**
> 
> You must generate this outline STRICTLY using the information contained within my "Saved Notes" in this notebook. Do not invent technical facts, introduce external concepts, or hallucinate information that is not explicitly covered in my notes. If a topic isn't in the notes, do not include it.
> 
> **Style & Tone Guidelines (The "Head First" Way):**
> 
> - **Visual & Conversational:** The tone should be highly conversational, slightly goofy, and engaging. Speak directly to the reader ("You're probably wondering...", "Let's take a look under the hood...").
>     
> - **Humor & Metaphor:** Use analogies and metaphors heavily to explain technical concepts.
>     
> - **Recurring Elements:** Incorporate these classic "Head First" structural elements into the slide descriptions:
>     
>     - **"There are no dumb questions..."** (For Q&A style slides)
>         
>     - **"Brain Power"** (For exercises, thought experiments, or deep dives)
>         
>     - **"Fireside Chats"** (For comparing two differing concepts or components)
>         
>     - **Annotations/Sticky Notes:** Explicitly mention where handwritten annotations, arrows, or "sticky notes" should appear on the slide to point out key details.
>         
>     - **"Who does what?"** (For explaining components of a system)
>         
> 
> **Output Format Requirements:**
> 
> Please output a 15-slide outline. For _each_ slide, provide the following structure:
> 
> - **Slide [Number]: [Catchy Title]**
>     
> - **Layout Type:** (e.g., Title Slide, Two Column, Diagram with Callouts, Q&A, Quote)
>     
> - **Main Content/Text:** What the main text on the slide says (keep it brief, punchy, and in the Head First tone).
>     
> - **Visuals/Imagery:** Describe the images, diagrams, or visual gags required (e.g., "A photo of a chaotic server rack looking like spaghetti," "A confused junior developer scratching their head").
>     
> - **Head First Elements:** Describe the annotations, sticky notes, drawn arrows, or specific "Head First" segments (like "There are no dumb questions") that will appear on this slide.
>     
> - **Source Note:** Briefly mention which specific saved note(s) this slide's information was pulled from.
>     
> 
> Please generate the 15-slide outline now based _only_ on my saved notes.

### Why this prompt works for NotebookLM:

1. **Strictly Bounding the Source:** NotebookLM is grounded in your documents, but telling it "STRICTLY using the information contained within my 'Saved Notes'" forces it to prioritize those specific clippings over the general source documents.
    
2. **Defining the Persona:** It explicitly tells the AI to adopt the _Head First_ persona, rather than just asking for a presentation.
    
3. **Forcing the Structure:** By asking for "Visuals/Imagery" and specific "Head First Elements" (like sticky notes and annotations), you are generating the blueprint you will need to actually build the slides later.