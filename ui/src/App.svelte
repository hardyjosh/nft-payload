<script lang="ts">
  import BrowseNFTs from "./routes/BrowseNFTs.svelte";
  import MintNFT from "./routes/MintNFT.svelte";
  import { onMount, setContext } from "svelte";
  import type { AppAgentClient } from "@holochain/client";
  import { AppAgentWebsocket } from "@holochain/client";
  import { clientContext } from "./contexts";
  import ConnectWallet from "./lib/connect-wallet/ConnectWallet.svelte";
  import Header from "./lib/Header.svelte";
  import { Routes } from "./lib/types";
  import Home from "./routes/Home.svelte";
  import { FileStorageClient } from "@holochain-open-dev/file-storage/dist/file-storage-client";

  let client: AppAgentClient | undefined;
  let fileStorageClient: FileStorageClient | undefined;
  let loading = true;

  let activeRoute = Routes.Home;

  $: client, loading, fileStorageClient;

  onMount(async () => {
    // We pass '' as url because it will dynamically be replaced in launcher environments
    client = await AppAgentWebsocket.connect("", "nft-payload");
    fileStorageClient = new FileStorageClient(client, "nft_payload");
    loading = false;
  });

  setContext(clientContext, {
    getClient: () => client,
    getFileStorageClient: () => fileStorageClient,
  });
</script>

<main class="flex flex-col w-full p-4">
  {#if loading}
    <div
      class="display: flex; flex: 1; align-items: center; justify-content: center"
    >
      loading....
    </div>
  {:else}
    <Header bind:activeRoute />
    {#if activeRoute == Routes.Home}
      <Home />
    {:else if activeRoute == Routes.MintNFT}
      <MintNFT />
    {:else if activeRoute == Routes.ViewNFTs}
      <BrowseNFTs />
    {/if}
  {/if}
</main>
