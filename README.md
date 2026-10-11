# @three-ws/spatial-mcp

<!-- three.ws:badges -->
[![GitHub stars](https://img.shields.io/github/stars/nirholas/spatial-mcp?style=flat&logo=github)](https://github.com/nirholas/spatial-mcp/stargazers) [![Last commit](https://img.shields.io/github/last-commit/nirholas/spatial-mcp?style=flat)](https://github.com/nirholas/spatial-mcp/commits) [![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen?style=flat)](https://github.com/nirholas/spatial-mcp/pulls) [![AI agent friendly](https://img.shields.io/badge/AI%20agents-AGENTS.md%20%2B%20llms.txt-6d5dfc?style=flat)](https://github.com/nirholas/spatial-mcp/blob/HEAD/AGENTS.md)
<!-- /three.ws:badges -->


Validator, builder, and conformance fixtures for **Spatial MCP**, the open (CC0) shape for returning a live, interactive 3D scene as a first-class MCP tool result instead of a URL in text.

- Spec: [`specs/SPATIAL_MCP.md`](https://github.com/nirholas/three.ws/blob/main/specs/SPATIAL_MCP.md)
- Live reference renderer + paste-in checker: [three.ws/spatial-mcp](https://three.ws/spatial-mcp)
- Same module, importable without npm: `https://three.ws/spatial-mcp/spatial-validator.js`

Dependency-free. Runs in Node 18+, any browser, and any bundler.

## Install

```bash
npm install @three-ws/spatial-mcp
```

## Emit a conformant artifact

Put the artifact in your MCP tool result's `structuredContent`. Only `spatialMcpVersion`, `kind`, and `scene.glbUrl` are required; `buildSpatialArtifact` fills the recommended blocks and omits anything you don't provide.

```js
import { buildSpatialArtifact } from '@three-ws/spatial-mcp';

const spatial = buildSpatialArtifact({
	glbUrl: 'https://three.ws/avatars/xbot.glb',
	kind: 'rigged-model',
	prompt: 'a sci-fi robot',
	title: 'Sci-fi robot',
});

return {
	content: [{ type: 'text', text: 'Here is your 3D model.' }],
	structuredContent: { glbUrl: spatial.scene.glbUrl, spatial },
};
```

## Validate before you ship

`validateSpatialArtifact` returns actionable diagnostics, never a bare boolean: every problem names the offending `path` and how to fix it.

```js
import { validateSpatialArtifact } from '@three-ws/spatial-mcp';

const { valid, errors, warnings } = validateSpatialArtifact(spatial);
// errors:   [{ path: 'scene.glbUrl', message: 'required — must be an https URL to a .glb asset' }]
// warnings: [{ path: 'camera', message: 'recommended — include `{ autoRotate: true }` …' }]
```

## Data-minimization lint

The spec requires emitters to strip internal identifiers, wallet addresses, prices, and auth material from the artifact; that is what keeps the shape safe in crypto-free app stores. `lintSpatialMeta` flags the common violations. Findings are advisory and never affect validity.

```js
import { lintSpatialMeta } from '@three-ws/spatial-mcp';

lintSpatialMeta(artifact);
// [{ path: 'meta.session_id', message: 'looks like an internal/auth/coin field — …' }]
```

## Conformance fixtures

`fixtures/` is the spec's test corpus: real artifacts a conformant validator must accept, reject, or lint, with the required verdicts in `fixtures/manifest.json`. Point your own implementation at it:

```js
import manifest from '@three-ws/spatial-mcp/fixtures/manifest' with { type: 'json' };

for (const { file, valid, mustFlag = [] } of manifest.fixtures) {
	const artifact = await loadFixture(file); // read from node_modules/@three-ws/spatial-mcp/fixtures/<file>
	const result = myValidator(artifact);
	assert.equal(result.valid, valid, file);
	for (const path of mustFlag) assert(result.errors.some((e) => e.path === path), `${file} must flag ${path}`);
}
```

If your validator disagrees with a required verdict or misses a `mustFlag` path, it is not conformant. Fixture asset URLs use the reserved `assets.example` host: they exercise the validator and are not fetchable.

## API

| Export | What it does |
|---|---|
| `SPATIAL_MCP_VERSION` | the shape version this module implements (`'0.1'`) |
| `buildSpatialArtifact(fields)` | assemble a conformant artifact from what a generation tool already has |
| `validateSpatialArtifact(payload)` | `{ valid, version, errors, warnings }` with per-path diagnostics |
| `isConformantSpatialArtifact(payload)` | boolean convenience over the validator |
| `lintSpatialMeta(payload)` | advisory data-minimization findings, `[{ path, message }]` |

## Rendering

This package is emit-and-validate only. The framework-free reference renderer lives at [`public/spatial-mcp/spatial-renderer.js`](https://github.com/nirholas/three.ws/blob/main/public/spatial-mcp/spatial-renderer.js) (importable at `https://three.ws/spatial-mcp/spatial-renderer.js`) and displays any conformant artifact, including one transformed from a foreign tool result.

## License

CC0-1.0 ([LICENSE](LICENSE)). The spec and this implementation are public domain: reimplement, extend, and ship them anywhere.

<!-- three.ws:growth -->
## Support the project

If spatial-mcp saves you time, **[star it on GitHub](https://github.com/nirholas/spatial-mcp)**. Stars are how other developers and AI agents find the repositories worth trusting, and they cost you one click.

Know someone who would use it? [Post on X](https://twitter.com/intent/tweet?text=spatial-mcp%3A%20Validator%2C%20builder%2C%20and%20conformance%20fixtures%20for%20Spatial%20MCP%3A%20the%20open%20(CC0)%20shape%20for%20returning%20a&url=https%3A%2F%2Fgithub.com%2Fnirholas%2Fspatial-mcp) · [Share on Bluesky](https://bsky.app/intent/compose?text=spatial-mcp%3A%20Validator%2C%20builder%2C%20and%20conformance%20fixtures%20for%20Spatial%20MCP%3A%20the%20open%20(CC0)%20shape%20for%20returning%20a%20https%3A%2F%2Fgithub.com%2Fnirholas%2Fspatial-mcp) · [Share on LinkedIn](https://www.linkedin.com/sharing/share-offsite/?url=https%3A%2F%2Fgithub.com%2Fnirholas%2Fspatial-mcp) · [Submit to Hacker News](https://news.ycombinator.com/submitlink?u=https%3A%2F%2Fgithub.com%2Fnirholas%2Fspatial-mcp&t=spatial-mcp%3A%20Validator%2C%20builder%2C%20and%20conformance%20fixtures%20for%20Spatial%20MCP%3A%20the%20open%20(CC0)%20shape%20for%20returning%20a) · [Share on Reddit](https://www.reddit.com/submit?url=https%3A%2F%2Fgithub.com%2Fnirholas%2Fspatial-mcp&title=spatial-mcp%3A%20Validator%2C%20builder%2C%20and%20conformance%20fixtures%20for%20Spatial%20MCP%3A%20the%20open%20(CC0)%20shape%20for%20returning%20a)

## Built for AI agents too

Coding agents and LLM tooling can read this repo directly: [AGENTS.md](./AGENTS.md), [llms.txt](./llms.txt), [llms-full.txt](./llms-full.txt). Point an agent at `https://github.com/nirholas/spatial-mcp` and it has the context it needs.

## More from the same author

- [All repositories by nirholas](https://github.com/nirholas/nirholas#readme): the full catalog, grouped by topic
- [three.ws](https://three.ws): the platform for 3D AI agents with Solana wallets, a skill marketplace and x402 payments
- Questions or ideas: [open an issue](https://github.com/nirholas/spatial-mcp/issues) or [start a discussion](https://github.com/nirholas/spatial-mcp/discussions)

## Contributors

[![Contributors](https://contrib.rocks/image?repo=nirholas/spatial-mcp)](https://github.com/nirholas/spatial-mcp/graphs/contributors)

## Star history

[![Star History Chart](https://api.star-history.com/svg?repos=nirholas/spatial-mcp&type=Date)](https://www.star-history.com/#nirholas/spatial-mcp&Date)

<!-- /three.ws:growth -->

<!-- three.ws:ecosystem -->
## Part of three.ws

[three.ws](https://three.ws) is a platform for 3D AI agents with Solana wallets: avatars, a skill marketplace, x402 payments and more than seventy MCP servers. `@three-ws/spatial-mcp` is one package from it.

- Documentation site: https://nirholas.github.io/spatial-mcp/
- npm: https://www.npmjs.com/package/@three-ws/spatial-mcp
- Canonical source: https://github.com/nirholas/three.ws (this repository is a generated mirror of `packages/spatial-mcp`)
- Agent-readable summary: [llms.txt](./llms.txt) and [AGENTS.md](./AGENTS.md)
- Issues and ideas: https://github.com/nirholas/spatial-mcp/issues
<!-- /three.ws:ecosystem -->
