# mikser-io-whitebox

WhiteBox integration for [Mikser](https://github.com/almero-digital-marketing/mikser-io). Pushes processed entities to a WhiteBox `feed` service and synchronises a watched folder with a WhiteBox `storage` service.

For projects that publish into a WhiteBox-backed downstream — a multi-site network, an editorial backend, or an external content consumer. The push happens inside mikser's normal cycle (no separate sync job, no cron, no out-of-band drift) and respects `--clear` for a clean rebuild of the remote state.

## Install

```bash
npm install mikser-io-whitebox
```

## Usage

```js
// mikser.config.js
export default {
  plugins: ['whitebox'],
  whitebox: {
    context: 'my-project',
    services: {
      feed: {
        url: 'https://feed.example.com',
        token: 'FEED_TOKEN',
        expire: '10 days',
        match: (entity) => entity.type === 'document'
      },
      storage: {
        url: 'https://storage.example.com',
        token: 'STORAGE_TOKEN',
        storageFolder: 'storage',
        expire: '10 days',
        match: (entity) => entity.id.startsWith('/storage/')
      }
    }
  }
}
```

Either service is optional — omit `services.feed` or `services.storage` to disable that half. When `context` is not set, the plugin falls back to a per-machine id (`machineId_hostname_username`).

### Feed

Catalogs entities matching `feed.match` (default: `entity.type === 'document'`) into the WhiteBox feed on every `processed` phase, and expires/clears the cache after each run.

### Storage

Watches `storageFolder` (default `storage/`) and uploads matching entities — by source on `processed`, and by render output on `finalize`. `storage.match` defaults to `entity.id` containing `/storage/`. Imports existing files on startup; with `--clear` the remote storage is wiped first.

## License

MIT
