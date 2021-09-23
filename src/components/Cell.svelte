<script>
  export let value;
  export let revealed;

  export let handleReveal;

  import { css } from '../../stitches.config';
  import { keyframes } from '@stitches/core';

  const reveal = keyframes({
  "0%": {
    transform: "scale(1)",
    color: "transparent",
    backgroundColor: "$primary",
  },
  "50%": {
    transform: "scale(1.3)",
    color: "transparent",
    backgroundColor: "$primary",
  },
  "100%": { transform: "scale(.9)", color: "$text" },
});

  const cell = css({
    //position and zindex so this is displayed above the grid lines
  position: "relative",
  zIndex: "1",

  borderRadius: "4px",
  width: "100%",
  height: "100%",
  color: "$text",
  userSelect: "none",

  display: "flex",
  alignItems: "center",
  justifyContent: "center",

  "@media (prefers-reduced-motion: no-preference)": {
    transitionDuration: "150ms",
    transitionTimingFunction: "cubic-bezier(0.4, 0.14, 0.3, 1)",

    animationTimingFunction: "cubic-bezier(0.4, 0.14, 0.3, 1)",
  },

  variants: {
    variant: {
      revealed: {
        "@media (prefers-reduced-motion: no-preference)": {
          animation: `${reveal} 240ms`,
        },
      },
      hidden: {
        cursor: "pointer",
        transform: "scale(1.1)",
        borderRadius: "3px",
        backgroundColor: "$primary",

        "&:focus": {
          transform: "scale(1)",
          backgroundColor: "$primaryFocus",
        },
      },
      flagged: {
        transform: "scale(0.8)",
        backgroundColor: "$flagged",
        color: "$flagColor",
      },
    },
    bomb: {
      true: {},
    },
  },

  compoundVariants: [
    {
      variant: "revealed",
      bomb: true,
      css: {
        transform: "scale(0.8)",
        backgroundColor: "$bombBackground",
        borderRadius: "3px",
      },
    },
  ],
  })

  const box = css({
    position: "relative",
    zIndex: "1",

    width: "3rem",
    height: "3rem",
  })
</script>

<div class={box()}>
  <div tabIndex={0} class={cell({variant: revealed ? 'revealed' : "hidden"})} on:click={handleReveal} on:keydown={(e) => {
    if (e.code === "Enter" || e.code === "Space") {
      // this will most likely become a switch case when I try to handle navigation with the arrow keys
      handleReveal();
    }
  }}>
    {#if revealed}
      {#if value == 9}
        *
      {:else if value == 0}
        { " "}
      {:else}
        {value}
        {/if}
    {:else}
      {" "}
    {/if}
  </div>
</div>