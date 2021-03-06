

## v3.4.0 (TBD)

**v3.4.0 is not yet released.**

### Changed(Breaking Changes)

- Drop [ACIs from official release](https://github.com/coreos/etcd/pull/9059).
  - [AppC was officially suspended](https://github.com/appc/spec#-disclaimer-), as of late 2016.
  - [`acbuild`](https://github.com/containers/build#this-project-is-currently-unmaintained) is not maintained anymore.
  - `*.aci` files are not available from `v3.4` release.
- Exit on [empty hosts in advertise URLs](https://github.com/coreos/etcd/pull/8786).
  - Address [advertise client URLs accepts empty hosts](https://github.com/coreos/etcd/issues/8379).
  - e.g. `--advertise-client-urls=http://:2379`.
  - e.g. `--initial-advertise-peer-urls=http://:2380`.
- Exit on [shadowed environment variables](TODO).
  - Address [error on shadowed environment variables](https://github.com/coreos/etcd/issues/8380).
- Migrate dependency management tool from `glide` to [`golang/dep`](https://github.com/coreos/etcd/pull/9155).
  - <= 3.3 puts `vendor` directory under `cmd/vendor` directory to [prevent conflicting transitive dependencies](https://github.com/coreos/etcd/issues/4913).
  - 3.4 moves `cmd/vendor` directory to `vendor` at repository root.
  - Remove recursive symlinks in `cmd` directory.
  - Now `go get/install/build` on `etcd` packages (e.g. `clientv3`, `tools/benchmark`) enforce builds with etcd `vendor` directory.
- Reorganize [internal packages](https://github.com/coreos/etcd/issues/9220).
  - Move `"github.com/coreos/etcd/alarm"` to [`"github.com/coreos/etcd/internal/alarm"`](https://github.com/coreos/etcd/pull/9234).
  - Move `"github.com/coreos/etcd/compactor"` to [`"github.com/coreos/etcd/internal/compactor"`](https://github.com/coreos/etcd/pull/9234).
  - Move `"github.com/coreos/etcd/discovery"` to [`"github.com/coreos/etcd/internal/discovery"`](https://github.com/coreos/etcd/pull/9233).
  - Move `"github.com/coreos/etcd/snap"` to [`"github.com/coreos/etcd/internal/raftsnap"`](https://github.com/coreos/etcd/pull/9211).

### Added(API)

- Add [`snapshot`](https://github.com/coreos/etcd/pull/9118) package for snapshot restore/save operations.
- Add [`watch_id` field to `etcdserverpb.WatchCreateRequest`](https://github.com/coreos/etcd/pull/9065), allow user-provided watch ID to `mvcc`.
  - Corresponding `watch_id` is returned via `etcdserverpb.WatchResponse`, if any.
- Add [`raftAppliedIndex` field to `etcdserverpb.StatusResponse`](https://github.com/coreos/etcd/pull/9176) for current Raft applied index.
- Add [`errors` field to `etcdserverpb.StatusResponse`](https://github.com/coreos/etcd/pull/9206) for server-side error.
  - e.g. `"etcdserver: no leader", "NOSPACE", "CORRUPT"`

### Added(v3 `etcdctl`)

- Add [`check datascale`](https://github.com/coreos/etcd/pull/9185) command.
- Add ["raft applied index" to `endpoint status`](https://github.com/coreos/etcd/pull/9176).
- Add ["errors" to `endpoint status`](https://github.com/coreos/etcd/pull/9206).

### Package `raft`

- Improve [Raft `becomeLeader` and `stepLeader`](https://github.com/coreos/etcd/pull/9073) by keeping track of latest `pb.EntryConfChange` index.
  - Previously record `pendingConf` boolean field scanning the entire tail of the log, which can delay hearbeat send.
- Add [`raft.ErrProposalDropped`](https://github.com/coreos/etcd/pull/9067).
  - Now `(r *raft) Step` returns `raft.ErrProposalDropped` if a proposal has been ignored.
  - e.g. a node is removed from cluster, [ongoing leadership transfer](https://github.com/coreos/etcd/issues/8975), etc.
- Fix [missing learner nodes on `(n *node) ApplyConfChange`](https://github.com/coreos/etcd/pull/9116).

