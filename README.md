# CampusIQ

**Smart Campus Resource Optimization System** — a working prototype that
senses room occupancy, electricity and water use on a campus, predicts what
each will need next, flags anomalies like leaks or equipment left on, and
recommends which classes to consolidate so whole blocks can be powered down.

Built for the Innovators Conclave-style "Smart Campus Resource Optimization
System" problem statement: smart classroom allocation, real-time occupancy
monitoring, energy/water tracking, predictive analysis, abnormal-consumption
alerts, and an administrative dashboard.

No hardware is required to see it work — a deterministic simulator stands in
for sensors until real ones are connected (see [Real hardware](#real-hardware)).

## Quick start

```bash
git clone https://github.com/<your-username>/CampusIQ.git
cd CampusIQ
pip install -r requirements.txt
python tools/run_server.py 8000
```

Open `http://localhost:8000`. The dashboard connects live (Server-Sent
Events, with an automatic polling fallback) and starts updating within a
couple of seconds — no API keys, no cloud account, no build step.

Try the **Try it live** buttons on the dashboard to inject a simulated leak
or an "AC left on" event and watch an alert and a recommendation appear.
See [`docs/DEMO_SCRIPT.md`](docs/DEMO_SCRIPT.md) for a 60-second run-through
written for presenting this on stage.

## Run the tests

```bash
python -m unittest discover -s tests -v
```

15 tests cover the simulator, the prediction/anomaly model, the room
consolidation allocator, and the engine end to end.

## Check the prediction model's accuracy

```bash
python tools/train_report.py
```

Trains on 11 simulated days, evaluates on 3 held-out days, and prints mean
absolute error against a naive (overall-average) baseline per entity — on
the bundled campus this comes out 30–85% lower than the naive baseline,
without any external ML framework.

## What's in the repo

```
campusiq/         core Python package — config, simulator, models, engine, API server
web/               the dashboard (plain HTML/CSS/JS, no build step)
tools/             run_server.py, train_report.py
tests/             unit tests (python -m unittest)
firmware/          reference ESP32 sketch for real sensors
docs/              architecture notes and the demo script
.github/workflows/ CI: runs the test suite on every push
```

See [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) for how the pieces fit
together and why each modelling choice was made.

## Features → code

| Problem statement feature | Where it lives |
|---|---|
| Smart classroom/lab allocation | `campusiq/models.py: consolidate_blocks` |
| Real-time occupancy monitoring | `campusiq/simulator.py`, dashboard room heatmap |
| Energy and water consumption tracking | `campusiq/engine.py: snapshot`, dashboard KPIs |
| Predictive analysis of resource demand | `campusiq/models.py: DemandModel` |
| Alerts for abnormal/excessive consumption | `campusiq/engine.py: _detect`, dashboard Alerts panel |
| Administrative dashboard with analytics | `web/` |

## Real hardware

The simulator and real sensors are interchangeable at the entity level.
`POST /api/ingest {"entity": "A101", "value": 12}` overrides the simulator
for that room/block/tank for a short TTL — bring one ESP32 online and only
that entity stops being simulated. See
[`firmware/campusiq_esp32/`](firmware/campusiq_esp32/) for a reference
Arduino sketch (PIR occupancy + PZEM-004T energy) and
[`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md#swapping-the-simulator-for-real-hardware)
for how it plugs in.

## Author

Om Manikandan S · B.Tech AI&DS · Muthayammal Engineering College

## License

MIT — see [`LICENSE`](LICENSE).
