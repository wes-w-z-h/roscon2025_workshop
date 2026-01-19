# Zenoh for ROS 2 - Notes

## Running Without a Router (Peer-to-Peer)

### Peer as Hub Configuration
To run nodes without a router, one peer needs to act as the hub by listening for connections:

**Hub peer (e.g., talker):**
```bash
export RMW_IMPLEMENTATION=rmw_zenoh_cpp
export ZENOH_CONFIG_OVERRIDE='mode="peer";listen/endpoints=["tcp/127.0.0.1:7447"];connect/endpoints=[]'
ros2 run demo_nodes_cpp talker
```

**Connecting peer (e.g., listener):**
```bash
export RMW_IMPLEMENTATION=rmw_zenoh_cpp
export ZENOH_CONFIG_OVERRIDE='mode="peer";connect/endpoints=["tcp/127.0.0.1:7447"];listen/endpoints=[]'
ros2 run demo_nodes_cpp listener
```

**Note**: As long as the peers `connect/endpoints` match other peers `listen/endpoints` they will be able to communicate doesn't matter what ros node runs.

---

## Best Practices

### Configuration Management
1. **Copy defaults first**: Start from `DEFAULT_RMW_ZENOH_*_CONFIG.json5`
2. **Version control**: Ensure compatibility with your ROS distro and `rmw_zenoh` version
3. **Environment variables**: Use `RMW_ZENOH_ROUTER_CONFIG_URI` and `RMW_ZENOH_SESSION_CONFIG_URI`
4. **Config Override**: Use `ZENOH_CONFIG_OVERRIDE="key/path/to/field1=value1;key/path/to/field2=value2"` to change specific params for a node without affecting the general session config

### Performance Optimization
1. **Enable shared memory** for all processes when possible
2. **Use peer-to-peer** for nodes on the same host
3. **Configure TCP buffers** for high-throughput scenarios
4. **Monitor `/dev/shm`** to verify shared memory usage

### Network Design
1. **Local**: Peer mode + single router
2. **Remote (multiple nodes)**: Router-to-router
3. **Remote (single node)**: Client mode directly to main router
4. **Secure remote**: mTLS with proper certificate management

---

## Quick Reference Commands

```bash
# Start router
ros2 run rmw_zenoh_cpp rmw_zenohd

# List shared memory usage
ls -lh /dev/shm/*.zenoh

# Test connectivity
ros2 run demo_nodes_cpp talker
ros2 run demo_nodes_cpp listener

# Check configuration
echo $RMW_ZENOH_ROUTER_CONFIG_URI
echo $RMW_ZENOH_SESSION_CONFIG_URI
```
