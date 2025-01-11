<script lang="ts">
  import { twMerge } from "tailwind-merge";

  // Define types for the card properties
  type CardType = "number" | "reverse" | "skip" | "wild" | "draw4" | "draw2";
  type CardColor = "red" | "blue" | "green" | "yellow";

  interface Card {
    type: CardType;
    color?: CardColor; // Optional for wild/draw4
    numberValue?: number; // Only for "number" cards
    wildColor?: CardColor; // Only for wild/draw4 when matched
  }

  export let card: Card;
  export let actionsDisabled: boolean = false;
  export let disableMouseEvents: boolean = false;
  export let isCardToMatch: boolean = false;

  // Color mappings
  const bgColorMap: Record<CardColor, string> = {
    red: "bg-red-500",
    blue: "bg-blue-500",
    green: "bg-green-500",
    yellow: "bg-yellow-500",
  };

  const textColorMap: Record<CardColor, string> = {
    red: "text-red-500",
    blue: "text-blue-500",
    green: "text-green-500",
    yellow: "text-yellow-500",
  };

  const ringColorMap: Record<CardColor, string> = {
    red: "ring-red-500",
    blue: "ring-blue-500",
    green: "ring-green-500",
    yellow: "ring-yellow-500",
  };

  // Derived styles
  $: bgTwColor =
    card.type === "wild" || card.type === "draw4"
      ? "bg-black"
      : card.color
        ? bgColorMap[card.color]
        : "bg-transparent";

  $: textTwColor =
    card.type === "wild" || card.type === "draw4"
      ? "text-black"
      : card.color
        ? textColorMap[card.color]
        : "text-transparent";

  $: shadowColor =
    card.type === "wild" || card.type === "draw4" ? "#fff" : "#000";

  $: heightOfWhite = card.type === "number" ? "h-5/6" : "h-2/3";

  // Function to get card text
  function getText(type: "shortened" | "full" = "full"): string | undefined {
    if (card.type === "number") {
      return card.numberValue?.toString();
    } else if (card.type === "reverse") {
      return type === "shortened" ? "R" : "Reverse";
    } else if (card.type === "skip") {
      return type === "shortened" ? "S" : "Skip";
    } else if (card.type === "draw4") {
      return type === "shortened" ? "Wild" : "Draw four";
    } else if (card.type === "wild") {
      return "Wild";
    } else if (card.type === "draw2") {
      return type === "shortened" ? "D" : "Draw two";
    }
  }
</script>

<button
  class={twMerge(
    "jb-card relative m-10 rounded transition-all",
    actionsDisabled && "pointer-events-none opacity-40",
    disableMouseEvents && "pointer-events-none",
    (card.type === "wild" || card.type === "draw4") &&
      isCardToMatch &&
      card.wildColor &&
      `ring-4 ${ringColorMap[card.wildColor]}`
  )}
  disabled={actionsDisabled}
>
  <div
    class={twMerge(
      `no-highlight relative z-50 w-28 rounded-md ${bgTwColor} backface-hidden flex h-40 flex-col items-center justify-center border-2 border-black px-4 transition-all`
    )}
  >
    <p
      class={`${
        card.type === "wild" || card.type === "draw4"
          ? "text-black"
          : "text-white"
      } absolute left-2 top-2 z-10 text-xl font-black`}
      style={`text-shadow: 2px 2px 0 ${shadowColor}, -1px -1px 0 ${shadowColor}, 1px -1px 0 ${shadowColor}, -1px 1px 0 ${shadowColor}, 1px 1px 0 ${shadowColor};`}
    >
      {getText()}
    </p>

    <p
      class={`${
        card.type === "wild" || card.type === "draw4"
          ? "text-black"
          : "text-white"
      } absolute bottom-2 right-2 text-xl font-black`}
      style={`text-shadow: 2px 2px 0 ${shadowColor}, -1px -1px 0 ${shadowColor}, 1px -1px 0 ${shadowColor}, -1px 1px 0 ${shadowColor}, 1px 1px 0 ${shadowColor};`}
    >
      {getText()}
    </p>

    {#if card.type === "draw4"}
      <div class={`w-full ${heightOfWhite} relative -z-10 grid grid-cols-2`}>
        <div class="h-full w-full rounded-tl-[75px] bg-red-500"></div>
        <div class="h-full w-full bg-blue-500"></div>
        <div class="h-full w-full bg-yellow-500"></div>
        <div class="h-full w-full rounded-br-[75px] bg-green-500"></div>
        <p
          style={`text-shadow: 2px 2px 0 #000, -1px -1px 0 ${shadowColor}, 1px -1px 0 ${shadowColor}, -1px 1px 0 ${shadowColor}, 1px 1px 0 ${shadowColor};`}
          class="absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 transform text-5xl font-black text-white"
        >
          {getText("shortened")}
        </p>
      </div>
    {/if}

    {#if card.type === "number" || card.type === "reverse" || card.type === "skip" || card.type === "draw2"}
      <div
        class={`w-full rounded-tl-[75px] bg-white ${heightOfWhite} flex items-center justify-center rounded-br-[75px]`}
      >
        <p
          class={`text-4xl font-black ${textTwColor}`}
          style={`text-shadow: 3px 3px 0 ${shadowColor}, -1px -1px 0 ${shadowColor}, 1px -1px 0 ${shadowColor}, -1px 1px 0 ${shadowColor}, 1px 1px 0 ${shadowColor};`}
        >
          {getText("shortened")}
        </p>
      </div>
    {/if}
  </div>
</button>
