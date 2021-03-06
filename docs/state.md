# The State Store

kops has the notion of a 'state store'; a location where we store the configuration of your cluster.  State is stored
here not only when you first create a cluster, but also you can change the state and apply changes to a running cluster.

Eventually, kubernetes services will also pull from the state store, so that we don't need to marshal all our
configuration through a channel like user-data.  (This is currently done for secrets and SSL keys, for example,
though we have to copy the data from the state store to a file where components like kubelet can read them).

The state store uses kops's VFS implementation, so can in theory be stored anywhere.  Currently storage on S3
is supported, but support for GCS is coming soon, along with encrypted storage.

The state store is just files; you can copy the files down and put them into git (or your preferred version
control system).

## {statestore}/config

One of the most important files in the state store is the top-level config file.  This file stores the main
configuration for your cluster (instance types, zones, etc)\

When you run `kops create cluster`, we create a state store entry for you based on the command line options you specify. 
For example, when you run with `--node-size=m4.large`, we actually set a line in the configuration
that looks like `NodeMachineType: m4.large`.

The configuration you specify on the command line is actually just a convenient short-cut to
manually editing the configuration.  Options you specify on the command line are merged into the existing
configuration. If you want to configure advanced options, or prefer a text-based configuration, you
may prefer to just edit the config file with `kops edit cluster`.

Because the configuration is merged, this is how you can just specify the changed arguments when
reconfiguring your cluster - for example just `kops create cluster` after a dry-run.
