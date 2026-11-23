# Swarm Lab

A browser-based, interactive teaching tool for **Particle Swarm Optimization (PSO)**. Built with plain HTML, CSS, JavaScript, and Canvas. No runtime dependencies or paid services are required.

## Live demo

[Open Swarm Lab](https://swarm-lab-pso-mg.rollingblanket64.chatgpt.site)

## Run locally

Requires Node.js 20+ for the development server and tests. The deployed app only requires a modern browser.

```sh
npm run dev
```

Open http://127.0.0.1:4173. No package installation or build step is needed. Set the `PORT` environment variable to use a different port.

Run the automated checks:

```sh
npm test
```

## Deploy on GitHub Pages

The app is a static website with no server-side state. The included workflow tests the project and publishes `dist/` when changes are pushed to `main`.

1. In the repository, open **Settings → Pages**.
2. Select **GitHub Actions** as the build and deployment source.
3. Push to `main`, or run **Deploy static lab to GitHub Pages** from the Actions tab.

GitHub Pages availability depends on repository visibility and the account plan. You can also serve `dist/` with any static hosting provider.

## How to use

1. Select Sphere, Rastrigin, or Rosenbrock and adjust swarm size, inertia, personal/social pull, seed, and iteration budget.
2. Click **Start new experiment** to apply settings. Reset repeats the current experiment with the same seed.
3. **Step** advances one of four phases: calculate velocity, move, evaluate, update memories. **Play/Pause** automates these phases at the chosen speed.
4. Select a particle on the map, from the inspector dropdown, or in the population table. Colored arrows and the **Force microscope** show its three influences. Expand the arithmetic for exact sampled random factors and substitutions.
5. Use previous phase or the timeline to replay completed states. Continuing from an older state replays the existing future before generating new states.
6. Read the convergence chart and export the run as JSON with parameters, random draws, and all completed phase states.
7. Open **Check the math** for a hand calculation, and **Field guide** for concepts and experiments.

Keyboard: Space toggles playback and Right Arrow steps when focus is outside an interactive control. Native controls retain their standard keyboard behavior. The inspector and numeric table provide alternatives to clicking Canvas particles.

## What makes it different

- A **Force microscope** connects vector arrows with actual numeric momentum, personal memory, and social knowledge terms.
- Four distinct computation phases expose delayed evaluation and synchronous best updates.
- Reproducible seeded experiments and a rewindable phase timeline support careful comparison.
- A one-click “remove social pull” experiment helps students test how sharing affects search.
- JSON exports preserve an auditable record, including exact random factors.

## Algorithm and conventions

For each particle and coordinate:

```
v_new = w*v + c1*r1*(personal_best - x) + c2*r2*(global_best - x)
x_new = x + v_new
```

This is synchronous global-best inertia-weight PSO. Every velocity in an iteration uses the previous global best. Independent random factors are sampled for each term, particle, and coordinate. A seeded Mulberry32 generator makes replay deterministic.

- All objectives are minimized in two dimensions.
- Sphere: `x²+y²`, domain `[-5,5]²`, true minimum 0 at `(0,0)`.
- Rastrigin: `20+x²+y²-10*cos(2πx)-10*cos(2πy)`, domain `[-5.12,5.12]²`, true minimum 0 at `(0,0)`.
- Rosenbrock: `(1-x)²+100*(y-x²)²`, domain `[-3,3]²`, true minimum 0 at `(1,1)`.
- Initial velocity: uniform ±5% of domain width.
- Velocity clamp: each component limited to 20% of domain width.
- Boundary: clamp position to domain and zero the affected velocity component.
- Personal/global memory changes only on strict improvements after evaluation.
- Stop at the selected iteration budget; completion does not guarantee the global minimum.
- Best-so-far values use a logarithmic chart, with 10⁻¹² as the plotting floor only. Reported values retain their true value.
- Heatmap colors use log-scaled objective values for Sphere/Rosenbrock and linear objective values for Rastrigin. Pale is lower; colors are normalized separately per landscape and cannot compare absolute values across problems.
- Force arrows depict the last velocity calculation before speed and boundary handling. The inspector explains when these limits apply.
- Settings take effect only with **Start new experiment**. The landscape title and formula always identify the active run.

## Hand calculation and verification

This original hand-worked example uses the standard equation, not a claimed published numerical example.

Given `x=(2,1)`, `v=(0.4,-0.2)`, `p=(1,1)`, `g=(0,0)`, `w=0.5`, `c1=1`, `c2=2`, `r1=(0.5,0.5)`, `r2=(0.25,0.25)`:

| Quantity | Hand calculation |
| --- | --- |
| Inertia | `0.5*(0.4,-0.2) = (0.2,-0.1)` |
| Personal | `1*0.5*((1,1)-(2,1)) = (-0.5,0)` |
| Social | `2*0.25*((0,0)-(2,1)) = (-1,-0.5)` |
| New velocity | `(0.2,-0.1)+(-0.5,0)+(-1,-0.5) = (-1.3,-0.6)` |
| New position | `(2,1)+(-1.3,-0.6) = (0.7,0.4)` |
| New fitness | `0.7²+0.4² = 0.65` |

Personal best changes from `(1,1)` (fitness 2) to `(0.7,0.4)` (fitness 0.65). Global best `(0,0)` remains unchanged. Neither clamp is triggered. The same `forces()` function powers the lab and verification, with independently specified expected constants and tolerance 10⁻¹⁰.

Eight automated tests check this arithmetic, known function minima, phase order, reproducibility, domain/speed limits, monotonic best memory, zero-force behavior, boundary handling, and default-run improvement. Run with Node's built-in test runner via `npm test`.

## Structure

```
dist/index.html      Semantic interface and explanatory content
dist/style.css       Responsive presentation
dist/engine.js       Pure algorithm, seeded RNG, worked-example check
dist/app.js          Playback, replay, map, table, chart and exports
tests/engine.test.mjs
server.mjs           Local static development server
.github/workflows/   GitHub Pages deployment workflow
```

## References and attribution

- A. P. Engelbrecht, *Computational Intelligence: An Introduction*, Chapter 16, [Particle Swarm Optimization](https://web2.qatar.cmu.edu/~gdicaro/15382-Spring18/additional/CompIntelligence-Engelbrecht-ch16.pdf): algorithm and conceptual reference.
- [VisuAlgo](https://visualgo.net/en/): inspiration for stepwise algorithm teaching only. No code or assets were copied.
- Interface, implementation, and worked arithmetic are original to this project.
- DM Sans and Manrope are loaded optionally from Google Fonts; system fonts work offline.

The simulation runs locally in the browser and sends no experiment data to a server.
