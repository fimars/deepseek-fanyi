<script lang="ts">
  import Button from "@/components/ui/button/button.svelte";
  import { open } from "@tauri-apps/plugin-dialog";
  import { readDir, BaseDirectory, type DirEntry } from "@tauri-apps/plugin-fs";

  let { onChange } = $props();

  let folder = $state("");
  let currentFile = $state("");
  let files = $state([] as DirEntry[]);

  async function openWorkspace() {
    const path = await open({
      multiple: false,
      directory: true,
    });
    await changeWorkSpace(path!);
  }

  async function changeWorkSpace(path: string) {
    folder = path;
    files = await readDir(path);
  }
  function changeCurrentFile(name: string) {
    currentFile = name;
    onChange(folder + "/" + name);
  }
</script>

<nav class="p-4">
  <Button onclick={openWorkspace}>Load Folder</Button>
  <div class="flex flex-col gap-1">
    {#if folder !== ""}
      <h2>Folder: {folder}</h2>
      <ul>
        <li><button aria-label="back" onclick="{() => {
          changeWorkSpace(folder.split("/").slice(0, -1).join("/"))
        }}">📁 ../</button></li>
        {#each files as item}
          <li class={item.name === currentFile ? "bg-slate-400" : ""}>
            <button
              aria-label="open-file"
              class="bg-none border-none"
              onclick={() => {
                if (item.isDirectory)
                  return changeWorkSpace(folder + "/" + item.name);
                changeCurrentFile(item.name);
              }}
            >
              {item.isDirectory ? "📁" : "📄"} {item.name}</button
            >
          </li>
        {/each}
      </ul>
    {/if}
  </div>
</nav>
