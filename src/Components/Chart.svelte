<script>
  import { onMount } from 'svelte';

  let points = [
    { x: 70, y: 180 },
    { x: 130, y: 180 },
    { x: 140, y: 80 },
    { x: 60, y: 80 }
  ];

  let points2 = [
    { x: 55, y: 50 },
    { x: 145, y: 50 },
    { x: 140, y: 80 },
    { x: 60, y: 80 }
  ];

  onMount(() => {
    const svg = document.getElementById('chart1');

    while (svg.firstChild) {
      svg.removeChild(svg.firstChild);
    }

    const polygon1 = document.createElementNS('http://www.w3.org/2000/svg', 'polygon');
    polygon1.setAttribute('points', points.map(p => `${p.x},${p.y}`).join(' '));
    polygon1.setAttribute('fill', '#d19d0f');
    polygon1.setAttribute('stroke', 'black');
    polygon1.setAttribute('stroke-width', '5');
    svg.appendChild(polygon1);

    const polygon2 = document.createElementNS('http://www.w3.org/2000/svg', 'polygon');
    polygon2.setAttribute('points', points2.map(p => `${p.x},${p.y}`).join(' '));
    polygon2.setAttribute('fill', 'white');
    polygon2.setAttribute('stroke', 'black');
    polygon2.setAttribute('stroke-width', '5');
    svg.appendChild(polygon2);
  });
</script>

<svg id="chart1" width="500" height="500" viewBox="0 0 500 500"></svg>

