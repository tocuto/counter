<script lang="ts">
import { Progressbar } from 'flowbite-svelte';
const start = new Date("2025-06-07T19:35:00Z");
const end = new Date("2025-08-08T13:35:00Z");
const total = end - start;
let time = "";
let progress = "";
let precision = 0;
const precisions = {
    20: 1,
    25: 2,
    30: 3,
    34: 4,
    37: 5,
    40: 6,
}
const updateTime = () => {
    let delta = end - new Date(new Date().toISOString());
    delta = delta / 1000; // seconds
    let seconds = Math.floor(delta % 60);
    let minutes = Math.floor(delta / 60 % 60);
    let hours = Math.floor(delta / 3600 % 24);
    let days = Math.floor(delta / 86400);
    if (seconds < 10) { seconds = "0" + seconds; }
    if (minutes < 10) { minutes = "0" + minutes; }
    if (hours < 10) { hours = "0" + hours; }
    time = days + "d " + hours + ":" + minutes + ":" + seconds;
}
const updateProgress = () => {
    const now = new Date(new Date().toISOString());
    let percent = (now - start) / total * 100;
    progress = percent.toString();
    precision = 0;
    for (const [threshold, prec] of Object.entries(precisions)) {
        if (percent > threshold && prec > precision) {
            precision = prec;
        }
    }
    updateTime();
}
updateProgress();
setInterval(updateProgress, 100);
</script>

<main class="w-full">
<h1>{time}</h1>
<div class="my-b4 w-full">
<Progressbar
{progress}
size="h-6"
color="indigo"
animate
{precision}
labelInside
labelInsideClass="font-medium rounded-full"
class="h-6"
/>
</div>
</main>

