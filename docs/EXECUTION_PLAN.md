# Road Condition Mapping execution plan

## Outcome and scope

Build an evidence-first maintenance review tool for four visual damage classes: longitudinal crack, transverse crack, alligator crack and pothole candidate. A user imports licensed observations, reviews predicted boxes and quality flags, exports a prioritized queue, and maps only legitimately located records. Priority is a review heuristic, not severity in millimeters, legal road condition, repair cost or a maintenance prescription. Route optimization, pavement engineering, automated dispatch and additional asset classes are excluded.

The technical finish is a public, usable software package with a truthful synthetic map demonstration and reproducible evaluation. It does not require a city contract, fleet hardware, private imagery, new photography or a customer-provided geospatial archive. Commercial readiness requires separate evidence of rights, location accuracy and workflow value.

## Capacity and sequencing

One portfolio project and one implementation issue are active, receiving 16 hours weekly from activation. Twenty packages contain 640 work hours; 160 reserve hours make 800 total, including a 20% reserve. Immediate tasks are eight or twelve hours. Later epics are divided into four bounded children before coding; children replace the epic estimate.

| Milestone | Work | Reserve | Cumulative hours | Due |
| --- | ---: | ---: | ---: | --- |
| M1 scope and contracts | 40 | 10 | 50 | Month 1 |
| M2 data and detector | 160 | 40 | 250 | Month 4 |
| M3 functional public release | 160 | 40 | 450 | Month 7 |
| M4 robustness and packaging | 160 | 40 | 650 | Month 10 |
| M5 maintained handoff | 120 | 30 | 800 | By month 12 |

Cumulative milestones require 3.1, 15.6, 28.1, 40.6 and 50 weeks respectively, including reserve. These targets fit the available calendar; queued time is excluded. Reviews reconcile hours and reduce optional model or integration work before consuming release capacity. Reserve covers failed experiments and uncertainty, not additional features.

## Data strategy and rights gate

Record source URL, license text/version, attribution, original identifiers, hashes, country, capture sequence and available geolocation. The source report documents conflicting RDD2022 notices: its primary Figshare record says CC BY, while a packaging repository states CC BY-SA. Do not download into the development corpus, train or redistribute until evidence resolves the applicable rights. The fallback is an owned procedural road renderer, generating cracks, pothole depressions, boxes, masks, camera motion and simulated coordinates automatically.

Mapillary integration is optional and subject to current access terms and attribution requirements. It is not a source of evaluation labels merely because images are available. OSM basemaps are optional; the default viewer uses an empty coordinate canvas with attribution on any added layers. Keep derived-data and raw-image rights separate. Technical delivery can proceed with synthetic scenes if real-data clearance fails, but real-world detection quality then remains unestablished. No per-example manual labeling or manual dataset curation is allowed.

## Stack and baseline

Use Python, PyTorch/torchvision Faster R-CNN with a ResNet-50 FPN backbone, OpenCV, NumPy, FastAPI, SQLite, Shapely, pyproj and a Leaflet client. Pin code, pretrained-weight terms and versions before adoption. Keep a simple edge/texture candidate baseline as a measurable comparator. Train a capped four-class detector on automatically labeled synthetic images; fine-tune on cleared existing annotations only if available. A geometry renderer produces its own labels without copying restricted images.

Separate modules implement source adapters, quality checks, detection, coordinate validation, aggregation and exports. Use a CPU-capable low-resolution inference path; training may use existing compute but must not require buying a GPU. A bounded lightweight-model comparison is scheduled if baseline latency fails. ONNX export is conditional on verified numerical parity, not a release dependency.

## Observation and location contracts

The observation schema includes schema_version, observation_id, asset_hash, source_kind, source_id, sequence_id, captured_at, license_ref, location_status and nullable location. Location stores longitude, latitude, horizontal_accuracy_m, coordinate_source and timestamp_delta_s. Unknown accuracy remains unknown. Detections reference observation_id, class_id, pixel box, confidence, quality flags and model_version. Review states never overwrite raw predictions.

Use EPSG:4326 at the API boundary and a declared local metric projection for distance calculations. Reject invalid ranges and ambiguous axis order. A camera coordinate describes an observation position, not the exact defect location; exports say so. Without calibrated geometry, no object projection onto the road is claimed. Unlocated inputs remain downloadable in CSV with blank coordinates and cannot become GeoJSON features.

Synthetic coordinates carry source_kind=synthetic and a persistent simulation banner. They never enrich real unlocated RDD images. Spatial aggregation uses an accuracy-aware radius, capture direction and evidence consistency; low-quality coordinates disable confident merging. Cross-session recurrence remains unknown without comparable timestamps, locations and visual support.

## Evaluation and holdouts

Freeze train/development/test manifests before training. For any approved real dataset, group by sequence or route and hold out an entire country when feasible. Where metadata is absent, cluster near-duplicate images by perceptual hash, keep clusters intact and explicitly limit route-generalization claims. Do not infer geographic truth from filenames or country labels. Existing public boxes support detection metrics, not map accuracy.

Synthetic tests hold out road layouts, texture families, camera trajectories and weather regimes, using at least 400 images from unseen sequences. Separate generator seeds alone are insufficient. Measure per-class AP at IoU 0.5, AP across IoU thresholds, recall at a fixed false-positive budget, calibration and quality-filter abstention. Bootstrap by sequence or duplicate group. Set a development threshold targeting 80% recall with at most one false positive per image, then report achieved test performance without retuning.

Validate mapping separately on synthetic trajectories with known locations: target zero unlocated records exported as geographic features, cluster precision above 95% and recall above 85% within the declared noise regime. Sweep coordinate error, opposite road directions, missing timestamps and recurrent observations. These targets are engineering gates, not measured results. Publish failures and narrow supported conditions if needed; real-image AP never validates geospatial deduplication.

## Release and deployment

M3 delivers a local application, sample synthetic route, image-only review fixture, detector checkpoint, evaluation card, CSV/GeoJSON exporters and a runnable quickstart. Every exported row carries provenance and every simulated map remains visibly marked. A fresh install must process input through evidence review and export without external credentials. Static example reports can accompany the functional local package.

Package as a localhost container with a persistent SQLite volume and content-addressed assets. Target four CPU cores, 16 GB RAM, 20 GB storage and p95 inference below three seconds at the documented input resolution; measure on named hardware. Set a 2,000-image development cap, 48 compute-hour training cap, upload limits and bounded job queues. If compute is inadequate, reduce the training subset and publish the weaker result. Paid hosting and paid API costs default to zero and require separate authorization.

## Workflow, privacy and maintenance

M4 validates three jobs: find an evidence image, distinguish located from unlocated findings, and export a traceable maintenance queue. Scripted walkthroughs establish technical usability; optional municipal or contractor feedback establishes external workflow evidence only if obtained. Record time per job and interpretation mistakes. Without external users or purchase intent, the commercial proposition remains unvalidated.

Use synthetic images in the public demo to avoid faces and plates. Optional real-image adapters need documented retention, redaction and access controls; automated blurring is not a guarantee of anonymity. M5 tests malformed uploads, path traversal, schema migration, restore and rollback. Logs avoid image content and sensitive coordinates by default. A four-week maintenance rehearsal measures update and incident effort within the budget. Estimate two hours monthly after handoff, excluding future feature development.

## Sources and governance

The basis is section 6 of “Research report 2: high-effort computer-vision projects for a solo builder” (2026-10-16), with execution governed by the published repository `AGENTS.md` protocol. Primary references are [RDD2022 Figshare](https://figshare.com/articles/dataset/RDD2022_-_The_multi-national_Road_Damage_Dataset_released_through_CRDDC_2022/21431547), the [conflicting packaging notice](https://github.com/large-road-damage-datalake/rdd2022-object-detection), [Mapillary licensing](https://help.mapillary.com/hc/en-us/articles/115001770409-CC-BY-SA-license-for-open-data) and [OSM attribution](https://www.openstreetmap.org/copyright). Verify exact terms at implementation. Dataset rights, evaluation, budget and architectural scope changes require an Astra review.
