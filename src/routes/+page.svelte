<script lang="ts">
  import * as Resizable from "$lib/components/ui/resizable/index.js";
  import { FilesList } from "@/components/files";
  import { readTextFile } from "@tauri-apps/plugin-fs";

  let currentFile = $state("")
  let content = $state("")

  async function openFile(path: string) {
    currentFile = path
    content = await readTextFile(currentFile);
  }
 </script>
  
  <main class="w-screen h-screen">
    <Resizable.PaneGroup direction="horizontal" class="">
      <Resizable.Pane defaultSize={30}>
       <FilesList onChange={openFile} />
      </Resizable.Pane>
      <Resizable.Handle />
      <Resizable.Pane defaultSize={70}>
        <div class="flex h-full flex-col gap-1">
         <span class="font-semibold">currentFile: {currentFile}</span>
         <code class="overflow-auto">
          {content}
         </code>
        </div>
      </Resizable.Pane>
     </Resizable.PaneGroup>
  </main>
