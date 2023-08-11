<script lang="ts">
	import { invalidate } from '$app/navigation';

	const refresh = async ({ target }: MouseEvent) => {
		const button = target as HTMLButtonElement;
		const defaultText = button.innerText;
		button.innerText = '갱신 중입니다.';
		button.disabled = true;
		try {
			await Promise.all([
				invalidate('/endpoint.json'),
				// Block almost instance refresh caused by disk cache.
				new Promise((resolve) => setTimeout(resolve, 1000)),
			]);
			button.innerText = '갱신되었습니다.';
		} catch {
			button.innerText = '갱신에 실패했습니다.';
		} finally {
			await new Promise((resolve) => setTimeout(resolve, 5000));
			button.innerText = defaultText;
			button.disabled = false;
		}
	};
</script>

<button on:click={refresh}>새로고침</button>
