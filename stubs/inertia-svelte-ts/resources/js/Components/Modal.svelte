<script lang="ts">
    import type { Snippet } from 'svelte';
    import { fade } from 'svelte/transition';
    import { cubicIn, cubicOut } from 'svelte/easing';

    // svelte-ignore non_reactive_update
    let dialog: HTMLDivElement;

    let {
        children,
        closeable = true,
        maxWidth = '2xl',
        onclose = () => {},
        show = false
    }: {
        children: Snippet;
        closeable?: boolean;
        maxWidth?: 'sm' | 'md' | 'lg' | 'xl' | '2xl';
        onclose?: () => void;
        show?: boolean;
    } = $props();

    let maxWidthClass = $derived(
        {
            sm: 'sm:max-w-sm',
            md: 'sm:max-w-md',
            lg: 'sm:max-w-lg',
            xl: 'sm:max-w-xl',
            '2xl': 'sm:max-w-2xl'
        }[maxWidth]
    );

    $effect(() => {
        if (show) document.body.appendChild(dialog);
        if (document) document.body.style.overflow = show ? 'hidden' : 'visible';

        return () => {
            if (document) document.body.style.overflow = 'visible';
        };
    });

    function close() {
        if (closeable) {
            onclose();
        }
    }

    function closeOnEscape(e: KeyboardEvent) {
        if (e.key === 'Escape' && show) {
            onclose();
        }
    }
</script>

<svelte:window on:keydown={closeOnEscape} />

{#if show}
    <div bind:this={dialog} style:display={show ? 'contents' : 'none'}>
        <div class="fixed inset-0 z-50 px-4 py-6 overflow-y-auto sm:px-0" scroll-region>
            <div
                in:fade={{ duration: 300, easing: cubicOut }}
                out:fade={{ duration: 200, easing: cubicIn }}
            >
                <!-- svelte-ignore a11y_consider_explicit_label -->
                <button class="fixed inset-0 transition-all transform-select-none" onclick={close}>
                    <div class="absolute inset-0 bg-gray-500 opacity-75 dark:bg-gray-900">
                        &nbsp;
                    </div>
                </button>
            </div>

            <div
                in:fade={{ duration: 300, easing: cubicOut }}
                out:fade={{ duration: 200, easing: cubicIn }}
            >
                <div
                    class="mb-6 bg-white dark:bg-gray-800 rounded-lg overflow-hidden shadow-xl transform transition-all sm:w-full sm:mx-auto {maxWidthClass}"
                >
                    {@render children()}
                </div>
            </div>
        </div>
    </div>
{/if}
