# Tempo Performance Test Results

**Date:** 2026-01-10
**Test Duration:** 75 minutes per profile
**Tempo Version:** 2.9.0
**Operator Version:** 0.19.0

---

## Test Configurations

| Profile | Ingestion Target | Query Rate | VUs (min/max) | Trace Profile |
|---------|------------------|------------|---------------|---------------|
| **1x-pico** | ~50 GB/day (0.6 MB/s) | 25 qps | 2/10 | small |
| **1x-extra-small** | ~100 GB/day (1.2 MB/s) | 25 qps | 5/20 | small |
| **1x-small** | ~500 GB/day (5.8 MB/s) | 50 qps | 20/80 | medium |
| **1x-medium** | ~2 TB/day (23 MB/s) | 70 qps | 50/200 | large |

### Deployment Configuration (All Profiles)

- **Variant:** TempoStack (distributed)
- **Replication Factor:** 2
- **Ingester Replicas:** 2
- **All Other Components:** 1 replica each
- **Retention:** 48 hours
- **Max Block Duration:** 10 minutes

---

## Resource Consumption Summary

### Total Resource Usage (P99 over 75 min)

| Profile | Memory P99 | Memory Max | Memory Avg | CPU P99 | CPU Max | CPU Avg |
|---------|------------|------------|------------|---------|---------|---------|
| **1x-pico** | 12.6 GB | 13.7 GB | 7.2 GB | 1.5 cores | 1.6 cores | 0.4 cores |
| **1x-extra-small** | 13.6 GB | 13.8 GB | 8.8 GB | 3.0 cores | 3.4 cores | 0.6 cores |
| **1x-small** | 24.0 GB | 25.1 GB | 15.5 GB | 4.2 cores | 4.5 cores | 1.3 cores |
| **1x-medium** | 32.8 GB | 32.9 GB | 23.5 GB | 17.3 cores | 17.9 cores | 12.0 cores |

### Resource Usage by Component (P99)

#### Memory (GB)

| Component | Pico | Extra-Small | Small | Medium |
|-----------|------|-------------|-------|--------|
| **Ingester** | 8.3 | 10.0 | 12.1 | 18.8 |
| **Compactor** | 6.4 | 6.7 | 9.8 | 13.6 |
| **Querier** | 0.5 | 1.9 | 4.0 | 4.5 |
| **Query-Frontend** | 1.3 | 1.3 | 1.3 | 1.3 |
| **Distributor** | 0.05 | 0.06 | 0.07 | 0.06 |
| **Gateway** | 0.08 | 0.11 | 0.15 | 0.13 |

#### CPU (cores)

| Component | Pico | Extra-Small | Small | Medium |
|-----------|------|-------------|-------|--------|
| **Ingester** | 1.46 | 2.23 | 2.75 | 10.62 |
| **Compactor** | 0.00 | 0.24 | 0.51 | 0.82 |
| **Querier** | 0.68 | 1.42 | 3.52 | 7.18 |
| **Query-Frontend** | 0.15 | 0.19 | 0.46 | 1.16 |
| **Distributor** | 0.16 | 0.28 | 0.88 | 2.35 |
| **Gateway** | 0.31 | 0.51 | 0.97 | 5.10 |

---

## Throughput Achieved

| Profile | Ingestion Rate | Query Rate | Total Iterations (Ingestion) |
|---------|----------------|------------|------------------------------|
| **1x-pico** | 103 iters/s | 25 qps | ~463k |
| **1x-extra-small** | 206 iters/s | 25 qps | ~926k |
| **1x-small** | 994 iters/s | 50 qps | ~4.5M |
| **1x-medium** | 3937 iters/s | 100 qps | ~17.7M |

---

## Suggested T-Shirt Sizes

Based on observed P99 resource usage with **25% headroom** for production safety margins.

### XS (Extra Small) - Up to 100 GB/day

```yaml
template:
  ingester:
    replicas: 2
    resources:
      requests:
        memory: 8Gi
        cpu: 1500m
      limits:
        memory: 12Gi
        cpu: 3000m
  compactor:
    replicas: 1
    resources:
      requests:
        memory: 4Gi
        cpu: 200m
      limits:
        memory: 8Gi
        cpu: 500m
  querier:
    replicas: 1
    resources:
      requests:
        memory: 1Gi
        cpu: 1000m
      limits:
        memory: 2Gi
        cpu: 2000m
  queryFrontend:
    replicas: 1
    resources:
      requests:
        memory: 1Gi
        cpu: 200m
      limits:
        memory: 2Gi
        cpu: 500m
  distributor:
    replicas: 1
    resources:
      requests:
        memory: 128Mi
        cpu: 200m
      limits:
        memory: 256Mi
        cpu: 500m
  gateway:
    replicas: 1
    resources:
      requests:
        memory: 128Mi
        cpu: 400m
      limits:
        memory: 256Mi
        cpu: 1000m
```

**Total Resources:**
- Requests: ~23 Gi memory, ~5 cores
- Limits: ~37 Gi memory, ~10 cores

---

### S (Small) - Up to 500 GB/day

```yaml
template:
  ingester:
    replicas: 2
    resources:
      requests:
        memory: 10Gi
        cpu: 2000m
      limits:
        memory: 16Gi
        cpu: 4000m
  compactor:
    replicas: 1
    resources:
      requests:
        memory: 6Gi
        cpu: 400m
      limits:
        memory: 12Gi
        cpu: 1000m
  querier:
    replicas: 1
    resources:
      requests:
        memory: 3Gi
        cpu: 2500m
      limits:
        memory: 5Gi
        cpu: 4000m
  queryFrontend:
    replicas: 1
    resources:
      requests:
        memory: 1Gi
        cpu: 400m
      limits:
        memory: 2Gi
        cpu: 1000m
  distributor:
    replicas: 1
    resources:
      requests:
        memory: 128Mi
        cpu: 600m
      limits:
        memory: 256Mi
        cpu: 1000m
  gateway:
    replicas: 1
    resources:
      requests:
        memory: 192Mi
        cpu: 800m
      limits:
        memory: 256Mi
        cpu: 1500m
```

**Total Resources:**
- Requests: ~31 Gi memory, ~9 cores
- Limits: ~52 Gi memory, ~16 cores

---

### M (Medium) - Up to 2 TB/day

```yaml
template:
  ingester:
    replicas: 2
    resources:
      requests:
        memory: 16Gi
        cpu: 8000m
      limits:
        memory: 24Gi
        cpu: 12000m
  compactor:
    replicas: 1
    resources:
      requests:
        memory: 10Gi
        cpu: 600m
      limits:
        memory: 16Gi
        cpu: 1000m
  querier:
    replicas: 1
    resources:
      requests:
        memory: 4Gi
        cpu: 5000m
      limits:
        memory: 6Gi
        cpu: 8000m
  queryFrontend:
    replicas: 1
    resources:
      requests:
        memory: 1Gi
        cpu: 800m
      limits:
        memory: 2Gi
        cpu: 1500m
  distributor:
    replicas: 1
    resources:
      requests:
        memory: 128Mi
        cpu: 1500m
      limits:
        memory: 256Mi
        cpu: 3000m
  gateway:
    replicas: 1
    resources:
      requests:
        memory: 192Mi
        cpu: 4000m
      limits:
        memory: 256Mi
        cpu: 6000m
```

**Total Resources:**
- Requests: ~48 Gi memory, ~28 cores
- Limits: ~73 Gi memory, ~44 cores

---

## T-Shirt Size Summary Table

| Size | Ingestion | Memory (requests) | Memory (limits) | CPU (requests) | CPU (limits) |
|------|-----------|-------------------|-----------------|----------------|--------------|
| **XS** | ~100 GB/day | 23 Gi | 37 Gi | 5 cores | 10 cores |
| **S** | ~500 GB/day | 31 Gi | 52 Gi | 9 cores | 16 cores |
| **M** | ~2 TB/day | 48 Gi | 73 Gi | 28 cores | 44 cores |

---

## Key Observations

1. **Ingester is the primary memory consumer** - Accounts for 50-60% of total memory across all sizes due to WAL and in-memory trace buffering.

2. **Compactor has high baseline memory** - Uses 6-14 GB regardless of load due to batch processing of blocks.

3. **CPU scales linearly with throughput** - Especially noticeable in ingester and gateway components.

4. **Query-Frontend memory is constant** - Remains at ~1.3 GB across all load levels.

5. **Distributor is lightweight** - Minimal resource requirements (~64 MB memory) as it only routes requests.

6. **Gateway CPU increases significantly at scale** - From 0.3 cores (pico) to 5.1 cores (medium) due to TLS termination and request routing overhead.

---

## Recommendations

1. **Set memory limits 50-100% above requests** - Allows for burst handling while protecting the cluster.

2. **Consider horizontal scaling for queriers** - For read-heavy workloads at M size and above.

3. **Monitor ingester memory closely** - It's the most likely component to OOM under sustained load.

4. **Tune `max_block_duration`** - Current setting of 10m works well; increasing may reduce compactor pressure but increase ingester memory.

5. **Plan for compactor spikes** - Memory usage can spike during compaction cycles; ensure adequate headroom.
