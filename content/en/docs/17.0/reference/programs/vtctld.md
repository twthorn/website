---
title: vtctld
description: The Vitess Cluster Management Daemon
---

`vtctld` provides web and gRPC interfaces to manage a single Vitess cluster.
It is usually the first Vitess component to be started after a valid [global topology service](../../../user-guides/configuration-basic/global-topo/) has been created.

## `grpc-vtctld` &mdash; New as of v14

For the last several releases, `vtctld` has been transitioning to a newer gRPC service for well-typed cluster management requests.
To enable this newer service, include `"grpc-vtctld"` in the `--service_map` argument.

This is **required** to use programs such as [`vtadmin`](../vtadmin/) and [`vtctldclient`](../vtctldclient/).
While the old API and service are deprecated and will be removed in a future release, they are still required for certain cluster management commands (e.g. `OnlineDDL` and `Reshard`) as well as the vtctld web UI, so most users will want to include both in their `--service_map`.
This is demonstrated in the example usage below:

## Example Usage

The following example launches the `vtctld` daemon on port 15000:

```bash
export TOPOLOGY_FLAGS="--topo_implementation etcd2 --topo_global_server_address localhost:2379 --topo_global_root /vitess/global"
export VTDATAROOT="/tmp"

vtctld \
 $TOPOLOGY_FLAGS \
 --service_map 'grpc-vtctl,grpc-vtctld' \
 --backup_storage_implementation file \
 --file_backup_storage_root $VTDATAROOT/backups \
 --log_dir $VTDATAROOT/tmp \
 --port 15000 \
 --grpc_port 15999
```

## Options

| Name | Type | Definition |
| :------------------------------------ | :--------- | :----------------------------------------------------------------------------------------- |
| --action_timeout | duration | time to wait for an action before resorting to force (default 2m0s) |
| --alsologtostderr |  | log to standard error as well as files |
| --azblob_backup_account_key_file | string | Path to a file containing the Azure Storage account key; if this flag is unset, the environment variable VT_AZBLOB_ACCOUNT_KEY will be used as the key itself (NOT a file path) |
| --azblob_backup_account_name | string | Azure Storage Account name for backups; if this flag is unset, the environment variable VT_AZBLOB_ACCOUNT_NAME will be used |
| --azblob_backup_container_name | string | Azure Blob Container Name |
| --azblob_backup_parallelism | int | Azure Blob operation parallelism (requires extra memory when increased) (default 1) |
| --azblob_backup_storage_root | string | Root prefix for all backup-related Azure Blobs; this should exclude both initial and trailing '/' (e.g. just 'a/b' not '/a/b/') |
| --backup_engine_implementation | string | Specifies which implementation to use for creating new backups (builtin or xtrabackup). Restores will always be done with whichever engine created a given backup. (default "builtin") |
| --backup_storage_block_size | int | if backup_storage_compress is true, backup_storage_block_size sets the byte size for each block while compressing (default is 250000). (default 250000) |
| --backup_storage_compress | boolean | if set, the backup files will be compressed (default is true). |
| --backup_storage_implementation | string | which implementation to use for the backup storage feature |
| --backup_storage_number_blocks | int | if backup_storage_compress is true, backup_storage_number_blocks sets the number of blocks that can be processed, at once, before the writer blocks, during compression (default is 2). It should be equal to the number of CPUs available for compression (default 2) |
| --builtinbackup-file-read-buffer-size | uint | read files using an IO buffer of this many bytes. Golang defaults are used when set to 0. |
| --builtinbackup-file-write-buffer-size | uint | write files using an IO buffer of this many bytes. Golang defaults are used when set to 0. (default 2097152) |
| --builtinbackup_mysqld_timeout | duration | how long to wait for mysqld to shutdown at the start of the backup. (default 10m0s) |
| --builtinbackup_progress | duration | how often to send progress updates when backing up large files. (default 5s) |
| --catch-sigpipe | | catch and ignore SIGPIPE on stdout and stderr if specified |
| --cell | string | cell to use |
| --ceph_backup_storage_config | string | Path to JSON config file for ceph backup storage (default "ceph_backup_config.json") |
| --compression-engine-name | string | compressor engine used for compression. (default "pargzip") |
| --compression-level | int | what level to pass to the compressor. (default 1) |
| --consul_auth_static_file | string | JSON File to read the topos/tokens from. |
| --datadog-agent-host | string | host to send spans to. if empty, no tracing will be done |
| --datadog-agent-port | string | port to send spans to. if empty, no tracing will be done |
| --disable_active_reparents | boolean | if set, do not allow active reparents. Use this to protect a cluster using external reparents. |
| --emit_stats | boolean | true iff we should emit stats to push-based monitoring/stats backends |
| --external-compressor | string | command with arguments to use when compressing a backup. |
| --external-compressor-extension | string | extension to use when using an external compressor. |
| --external-decompressor |string | command with arguments to use when decompressing a backup. |
| --file_backup_storage_root | string | root directory for the file backup storage -- this path must be on shared storage to provide a global view of backups to all vitess components |
| --gcs_backup_storage_bucket | string | Google Cloud Storage bucket to use for backups |
| --gcs_backup_storage_root | string | root prefix for all backup-related object names |
| --grpc_auth_mode | string | Which auth plugin implementation to use (eg: static) |
| --grpc_auth_mtls_allowed_substrings | string | List of substrings of at least one of the client certificate names (separated by colon). |
| --grpc_auth_static_client_creds | string | when using grpc_static_auth in the server, this file provides the credentials to use to authenticate with server |
| --grpc_auth_static_password_file | string | JSON File to read the users/passwords from. |
| --grpc_ca | string | ca to use, requires TLS, and enforces client cert check |
| --grpc_cert | string | certificate to use, requires grpc_key, enables TLS |
| --grpc_compression | string | how to compress gRPC, default: nothing, supported: snappy |
| --grpc_crl | string | path to a certificate revocation list in PEM format, client certificates will be further verified against this file during TLS handshake |
| --grpc_enable_optional_tls | | enable optional TLS mode when a server accepts both TLS and plain-text connections on the same port |
| --grpc_enable_tracing | boolean | Enable GRPC tracing |
| --grpc_initial_conn_window_size | int | grpc initial connection window size |
| --grpc_initial_window_size | int | grpc initial window size |
| --grpc_keepalive_time | duration | After a duration of this time if the client doesn't see any activity it pings the server to see if the transport is still alive. (default 10s) |
| --grpc_keepalive_timeout | duration | After having pinged for keepalive check, the client waits for a duration of Timeout and if no activity is seen even after that the connection is closed. (default 10s) |
| --grpc_key | string | key to use, requires grpc_cert, enables TLS |
| --grpc_max_connection_age | duration | Maximum age of a client connection before GoAway is sent. (default 2562047h47m16.854775807s) |
| --grpc_max_connection_age_grace | duration | Additional grace period after grpc_max_connection_age, after which connections are forcibly closed. (default 2562047h47m16.854775807s) |
| --grpc_max_message_size | int | Maximum allowed RPC message size. Larger messages will be rejected by gRPC with the error 'exceeding the max size'. (default 16777216) |
| --grpc_port | int | Port to listen on for gRPC calls |
| --grpc_prometheus | boolean | Enable gRPC monitoring with Prometheus |
| --grpc_server_ca | string | path to server CA in PEM format, which will be combine with server cert, return full certificate chain to clients |
| --grpc_server_initial_conn_window_size | int | grpc server initial connection window size |
| --grpc_server_initial_window_size | int | grpc server initial window size |
| --grpc_server_keepalive_enforcement_policy_min_time | duration | grpc server minimum keepalive time (default 5m0s) |
| --grpc_server_keepalive_enforcement_policy_permit_without_stream | boolean | grpc server permit client keepalive pings even when there are no active streams (RPCs) |
| --jaeger-agent-host | string | host and port to send spans to. if empty, no tracing will be done |
| --keep_logs | duration | keep logs for this long (using ctime) (zero to keep forever) |
| --keep_logs_by_mtime | duration | keep logs for this long (using mtime) (zero to keep forever) |
| --lameduck-period | duration | keep running at least this long after SIGTERM before stopping (default 50ms) |
| --log_backtrace_at | value | when logging hits line file:N, emit a stack trace |
| --log_dir | string | If non-empty, write log files in this directory |
| --log_err_stacks | boolean | log stack traces for errors |
| --log_rotate_max_size | uint | size in bytes at which logs are rotated (glog.MaxSize) (default 1887436800) |
| --logtostderr | boolean | log to standard error instead of files |
| --manifest-external-decompressor | string | command with arguments to store in the backup manifest when compressing a backup with an external compression engine. |
| --max-stack-size | int | configure the maximum stack size in bytes (default 67108864) |
| --onclose_timeout | duration | wait no more than this for OnClose handlers before stopping (default 1ns) |
| --onterm_timeout | duration | wait no more than this for OnTermSync handlers before stopping (default 10s) |
| --opentsdb_uri | string | URI of opentsdb /api/put method |
| --pid_file | string | If set, the process will write its pid to the named file, and delete it on graceful shutdown. |
| --port | int | port for the server |
| --pprof | strings | enable profiling |
| --proxy_tablets | boolean | Setting this true will make vtctld proxy the tablet status instead of redirecting to them |
| --purge_logs_interval | duration | how often try to remove old logs (default 1h0m0s) |
| --remote_operation_timeout | duration | time to wait for a remote operation (default 30s) |
| --s3_backup_aws_endpoint | string | endpoint of the S3 backend (region must be provided) |
| --s3_backup_aws_region | string | AWS region to use (default "us-east-1") |
| --s3_backup_aws_retries | int | AWS request retries (default -1) |
| --s3_backup_force_path_style | boolean | force the s3 path style |
| --s3_backup_log_level | string | determine the S3 loglevel to use from LogOff, LogDebug, LogDebugWithSigning, LogDebugWithHTTPBody, LogDebugWithRequestRetries, LogDebugWithRequestErrors (default "LogOff") |
| --s3_backup_server_side_encryption | string | server-side encryption algorithm (e.g., AES256, aws:kms) |
| --s3_backup_storage_bucket | string | S3 bucket to use for backups |
| --s3_backup_storage_root | string | root prefix for all backup-related object names |
| --s3_backup_tls_skip_verify_cert | boolean | skip the 'certificate is valid' check for SSL connections |
| --schema_change_check_interval | duration | How often the schema change dir is checked for schema changes. This value must be positive; if zero or lower, the default of 1m is used. |
| --schema_change_controller | string | schema change controller is responsible for finding schema changes and responding to schema change events |
| --schema_change_dir | string | directory contains schema changes for all keyspaces. Each keyspace has its own directory and schema changes are expected to live in '$KEYSPACE/input' dir. e.g. test_keyspace/input/*sql, each sql file represents a schema change |
| --schema_change_replicas_timeout | duration | how long to wait for replicas to receive the schema change (default 10s) |
| --schema_change_user | string | The user who submits this schema change. |
| --security_policy | string | the name of a registered security policy to use for controlling access to URLs - empty means allow all for anyone (built-in policies: deny-all, read-only) |
| --service_map | value | comma separated list of services to enable (or disable if prefixed with '-') Example: grpc-queryservice |
| --sql-max-length-errors | int | truncate queries in error logs to the given length (default unlimited) |
| --sql-max-length-ui | int | truncate queries in debug UIs to the given length (default 512) (default 512) |
| --stats_backend | string | The name of the registered push-based monitoring/stats backend to use |
| --stats_combine_dimensions | string | List of dimensions to be combined into a single "all" value in exported stats vars |
| --stats_common_tags | strings | Comma-separated list of common tags for the stats backend. It provides both label and values. Example: label1:value1,label2:value2 |
| --stats_drop_variables | string | Variables to be dropped from the list of exported variables. |
| --stats_emit_period | duration | Interval between emitting stats to all registered backends (default 1m0s) |
| --stderrthreshold | value | logs at or above this threshold go to stderr (default 1) |
| --tablet_dir | string | The directory within the vtdataroot to store vttablet/mysql files. Defaults to being generated by the tablet uid. |
| --tablet_grpc_ca | string | the server ca to use to validate servers when connecting |
| --tablet_grpc_cert | string | the cert to use to connect |
| --tablet_grpc_crl | string | the server crl to use to validate server certificates when connecting |
| --tablet_grpc_key | string | the key to use to connect |
| --tablet_grpc_server_name | string | the server name to use to validate server certificate |
| --tablet_health_keep_alive | duration | close streaming tablet health connection if there are no requests for this long (default 5m0s) |
| --tablet_manager_grpc_ca | string | the server ca to use to validate servers when connecting |
| --tablet_manager_grpc_cert | string | the cert to use to connect |
| --tablet_manager_grpc_concurrency | int | concurrency to use to talk to a vttablet server for performance-sensitive RPCs (like ExecuteFetchAs{Dba,AllPrivs,App}) (default 8) |
| --tablet_manager_grpc_connpool_size | int | number of tablets to keep tmclient connections open to (default 100) |
| --tablet_manager_grpc_crl | string | the server crl to use to validate server certificates when connecting |
| --tablet_manager_grpc_key | string | the key to use to connect |
| --tablet_manager_grpc_server_name | string | the server name to use to validate server certificate |
| --tablet_manager_protocol | string | the protocol to use to talk to vttablet (default "grpc") |
| --tablet_protocol | string | how to talk to the vttablets (default "grpc") |
| --tablet_refresh_interval | duration | Tablet refresh interval. (default 1m0s) |
| --tablet_refresh_known_tablets | | Whether to reload the tablet's address/port map from topo in case they change. (default true) |
| --tablet_url_template | string | format string describing debug tablet url formatting. See the Go code for getTabletDebugURL() how to customize this. (default "http://{{.GetTabletHostPort}}") |
| --topo_consul_lock_delay | duration | LockDelay for consul session. (default 15s) |
| --topo_consul_lock_session_checks | string | List of checks for consul session. (default "serfHealth") |
| --topo_consul_lock_session_ttl | string | TTL for consul session. |
| --topo_consul_watch_poll_duration | duration | time of the long poll for watch queries. (default 30s) |
| --topo_etcd_lease_ttl | int | Lease TTL for locks and leader election. The client will use KeepAlive to keep the lease going. (default 30) |
| --topo_etcd_tls_ca | string | path to the ca to use to validate the server cert when connecting to the etcd topo server |
| --topo_etcd_tls_cert | string | path to the client cert to use to connect to the etcd topo server, requires topo_etcd_tls_key, enables TLS |
| --topo_etcd_tls_key | string | path to the client key to use to connect to the etcd topo server, enables TLS |
| --topo_global_root | string | the path of the global topology data in the global topology server |
| --topo_global_server_address | string | the address of the global topology server |
| --topo_implementation | string | the topology implementation to use |
| --topo_k8s_context | string | The kubeconfig context to use, overrides the 'current-context' from the config |
| --topo_k8s_kubeconfig | string | Path to a valid kubeconfig file. |
| --topo_k8s_namespace | string | The kubernetes namespace to use for all objects. Default comes from the context or in-cluster config |
| --topo_read_concurrency | int | Concurrency of topo reads. (default 32) |
| --topo_zk_auth_file | string | auth to use when connecting to the zk topo server, file contents should be <scheme>:<auth>, e.g., digest:user:pass |
| --topo_zk_base_timeout | duration | zk base timeout (see zk.Connect) (default 30s) |
| --topo_zk_max_concurrency | int | maximum number of pending requests to send to a Zookeeper server. (default 64) |
| --topo_zk_tls_ca | string | the server ca to use to validate servers when connecting to the zk topo server |
| --topo_zk_tls_cert | string | the cert to use to connect to the zk topo server, requires topo_zk_tls_key, enables TLS |
| --topo_zk_tls_key | string | the key to use to connect to the zk topo server, enables TLS |
| --tracer | string | tracing service to use (default "noop") |
| --tracing-enable-logging | | whether to enable logging in the tracing service |
| --tracing-sampling-rate | float | sampling rate for the probabilistic jaeger sampler (default 0.1) |
| --tracing-sampling-type | string | sampling strategy to use for jaeger. possible values are 'const', 'probabilistic', 'rateLimiting', or 'remote' (default "const") |
| --v | value | log level for V logs |
| --version |  | print binary version |
| --vmodule | value | comma-separated list of pattern=N settings for file-filtered logging |
| --vtctl_healthcheck_topology_refresh | duration | refresh interval for re-reading the topology (default 30s) |
| --vtctld_sanitize_log_messages | | When true, vtctld sanitizes logging. |
