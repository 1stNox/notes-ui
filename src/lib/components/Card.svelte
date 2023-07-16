<div class="flex flex-col justify-start items-start w-full h-48 p-2 bg-stone-200/75 rounded-lg drop-shadow-lg">
    <h1 class="text-xl pb-1">{note.title}</h1>
    <p>
        {note.text}
    </p>
</div>

<script lang="ts">
    import type Note from "../shared/models/note";

    export let note: Note;
</script>