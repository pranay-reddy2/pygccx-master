# pygccx

A Python wrapper I built around [CalculiX](http://www.calculix.de) and [Gmsh](http://gmsh.info/) to make running FEA simulations from Python a bit less painful.

The basic idea: you define your geometry and mesh in Gmsh, convert it to a CalculiX-compatible format, attach boundary conditions and material properties, then either export an input deck or run the solver directly — all from a Python script.

Still a work in progress. Works well enough for my use cases (mostly static structural analysis), but there are definitely rough edges. See the [known limitations](#known-limitations) section.

---

## What it does

- Build 3D geometry and meshes using the Gmsh Python API
- Convert Gmsh meshes to CalculiX node/element sets
- Define materials, boundary conditions, loads, and analysis steps in Python
- Write out a `.inp` file for CalculiX (CCX)
- Optionally run CCX directly and read back results (`.frd` / `.dat`)
- Some basic post-processing: von Mises stress, principal stresses, coordinate transforms

Supported analysis types: **static, buckling, frequency**. Only solid elements for now — no shells or beams yet.

---

## Requirements

- Python 3.10+
- [CalculiX CrunchiX (CCX) 2.22](http://www.calculix.de) — the FEA solver
- [CalculiX GraphiX (CGX)](http://www.calculix.de) — optional, for viewing results
- Dependencies: `numpy`, `scipy`, `gmsh`

---

## Installation

```bash
pip install -r requirements.txt
```

Or install directly from source:

```bash
pip install .
```

---

## Quick start

The best way to get started is to look at the `examples/` folder. The `beam/` example is probably the simplest entry point.

A rough sketch of how the workflow looks:

```python
import gmsh
from pygccx import Model

# 1. Build geometry in Gmsh
gmsh.initialize()
# ... your Gmsh geometry code here ...

# 2. Create a pygccx model and import the mesh
model = Model('my_model')
model.mesh.read_gmsh_mesh()

# 3. Define materials and sections
# ...

# 4. Set up load steps
# ...

# 5. Solve or export
model.solve()
```

> **Tip:** Using VS Code or PyCharm with type checking turned on helps a lot — most classes have docstrings and type hints so autocomplete actually works.

---

## Implemented keywords

Not everything in CalculiX is supported yet. Here's what's currently working:

**Model keywords:** `*AMPLITUDE`, `*BOUNDARY`, `*CONTACT PAIR`, `*COUPLING`, `*ELASTIC`, `*FRICTION`, `*MATERIAL`, `*PLASTIC`, `*RIGID BODY`, `*SOLID SECTION`, `*SPRING`, `*TIE`, and a bunch more.

**Step keywords:** `*STATIC`, `*BUCKLE`, `*FREQUENCY`, `*CLOAD`, `*NODE FILE`, `*EL FILE`, `*CONTACT FILE`, and others.

For the full list (and what's not yet supported), see [`unsupported_keywords.md`](unsupported_keywords.md).

---

## Known limitations

- Only solid elements are supported (no shells, beams, or membrane elements)
- Some CCX keywords aren't implemented — check `unsupported_keywords.md`
- Post-processing is pretty basic right now — for serious visualization, export to VTK and use Paraview
- Hasn't been tested on Windows much

---

## Planned / TODO

- [ ] Support remaining static analysis keywords
- [ ] Better error messages when CCX fails
- [ ] Post-processing inside Gmsh
- [ ] More examples (thermal, contact)

---

## Contributing

If you find a bug or want to add a keyword, feel free to open an issue or PR. I try to respond but no guarantees on turnaround time.