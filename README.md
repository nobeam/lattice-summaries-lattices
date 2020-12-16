# Lattice Summaries - lattice files

This repository is used to manage the lattices shown on the [lattice-summaries website](https://lattice-summaries.netlify.app/). Every time a new lattice file is pushed to this repository, it automatically gets converted into serval lattice file formats. The resulting lattice files are stored on the `generated` branch. The orignal lattice file is stored in the `originals` folder on the `main` branch.

## Convert and publish lattice files (maintainers only)

The lattices are converted using the [LatticeJSON package](https://github.com/nobeam/latticejson). To convert all lattice files into the different formats run: *(requires Python 3.8+ and [Poetry](https://python-poetry.org/))*

```
poetry install
poetry run doit
```

The resulting lattices files are saved to the `generated` folder and can be published by pushing this folder to the `generated` branch.

## Add a new Lattice

1. Clone this repository
2. Create a new branch (e.g. `goslawski/add-mls2-lattice`)
3. Check if your lattice file meets all the requirements. [(see lattice file requirements)](#lattice-file-requirements) 
4. Add your lattice file to the `originals/<namespace>` folder. [(see naming scheme)](#naming-scheme)
5. Add an entry in the `info.toml` file. [(see below)](#infotoml)
6. Commit your changes to your new branch.
    - The commit summary should be `add goslawski/mls2_<family>_v_<version>` for a newly added lattice.
    - The commit summary should be `update goslawski/mls2_<family>_v_<version>` in case a lattice file is updated.
7. Push your branch and create a [pull request](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request).
    - All pull request must be reviewed by someone else before merged.
    - All pull request must be squash merged. 

### info.toml

Meta information on the lattices is listed in the `info.toml` file. If you add a new lattice to this repo don't forget to add an entry to this file: 

```toml
# example entry

[[lattices]]
namespace = "goslawski"
name = "mls2_scaled-from-bessy2_v_1"
machine = "mls2"
title = "mls2 based on bessy2"
authors = ["Goslawski", "Max Mustermann"]
energy = 1200
simulations = ["apace", "elegant", "madx"]
labels = []
description = """
A possible design lattice for the MLS2 based on a scaled down version of BESSY 2
"""
```

Most of the attributes should be self-explanatory. A list of available labels is listed below:

> :memo: TODO@michael: add list of useful labels

## Lattice file requirements
### Problem

To leverage multiple simulation tools we have to convert lattice files into different formats. As these lattice file formats can contain tool-specific elements/attributes (and somethings are turing-complete scripting languages), a 1:1 translation between the different lattice file formats is not always possible.

### Solution

We agree on a restricted set of generic elements, which should be available in every simulation software and define a 1:1 mapping between these generic elements and the different lattice file formats. We should also avoid simulation-specific attributes (e.g. `nkicks`), which should be included in the run files. Control-flow like `if` statements or `for` loops are also not allowed. Only basic variable assigned and basic sequence manipulation (reflection and repetition) is permissiable. A detailed specification of allowed elements/attributes can be found below.

### List of generic elements and 1:1 mapping

| Generic Element | MAD-X      | elegant | TRACY | OPA |
| --------------- | ---------- | ------- | ----- | --- |
| Drift           | drift      | drif    |       |     |
| Dipole          | sbend      | csbend  |       |     |
| Quadrupole      | quadrupole | kquad   |       |     |
| Sextupole       | sextupole  | ksext   |       |     |
| Octupole        | octupole   | koct    |       |     |
| Cavity          | rfcavity   | rfca    |       |     |

#### Dipole

| Generic Attribute | MAD-X | elegant | elegant | TRACY | description                   |
| ----------------- | ----- | ------- | ------- | ----- | ----------------------------- |
| length            | l     | l       |         |       | length                        |
| angle             | angle | angle   |         |       | deflection angle              |
| e1                | e1    | e1      |         |       | entrance angle                |
| e2                | e2    | e2      |         |       | exit angle                    |
| k1                | k1    | k1      |         |       | geometric quadrupole strength |

### List of generic elements and 1:1 mapping

| Generic Element | MAD-X      | elegant | TRACY | OPA |
| --------------- | ---------- | ------- | ----- | --- |
| Drift           | drift      | drif    |       |     |
| Dipole          | sbend      | csbend  |       |     |
| Quadrupole      | quadrupole | kquad   |       |     |
| Sextupole       | sextupole  | ksext   |       |     |
| Octupole        | octupole   | koct    |       |     |
| Cavity          | rfcavity   | rfca    |       |     |

#### Dipole (hardware - first part, program second part) 

| Generic Attribute | MAD-X | elegant | TRACY | OPA | description                   |
| ----------------- | ----- | ------- | ----- | --- | ----------------------------- |
| length            | l     | l       |       |     | length                        |
| angle             | angle | angle   |       |     | deflection angle              |
| e1                | e1    | e1      |       |     | entrance angle                |
| e2                | e2    | e2      |       |     | exit angle                    |
| k1                | k1    | k1      |       |     | geometric quadrupole strength |

#### Quadrupole

| Generic Attribute | MAD-X | elegant | TRACY | OPA | description                   |
| ----------------- | ----- | ------- | ----- | --- | ----------------------------- |
| length            | l     | l       |       |     | length                        |
| k1                | k1    | k1      |       |     | geometric quadrupole strength |


#### Sextupole

| Generic Attribute | MAD-X | elegant | TRACY | OPA | description                  |
| ----------------- | ----- | ------- | ----- | --- | ---------------------------- |
| length            | l     | l       |       |     | length                       |
| k2                | k2    | k2      |       |     | geometric sextupole strength |


#### Octupole

| Generic Attribute | MAD-X | elegant | TRACY | OPA | description                 |
| ----------------- | ----- | ------- | ----- | --- | --------------------------- |
| length            | l     | l       |       |     | length                      |
| k3                | k2    | k3      |       |     | geometric octupole strength |

## Naming Scheme

Information like the energy, periodicity, number of bends per cell and other details (e.g. longitudinal gradients bend) which characterize a lattice will be included in the `info.toml` file, so it is not necessary that they are present in the filename.

As we want to distribute the lattice files over the web we have restrict us to the [unreserved URL characters `A-Za-z0-9.~-_`](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_in_a_URI).

### Schema

The naming schema of a lattice file is given by:

<pre><code><b>&ltnamespace&gt</b> / <b>&ltmachine&gt</b>_<b>&ltfamiliy&gt</b>_v_<b>&ltversion&gt</b></pre></code>

A name is built up out of different `<identifiers>` which are separated by a `_`. Allowed characters within an `<identifier>` are therefore `A-Za-z0-9.~-`. 

> :warning: Note that `_` is not allowed!

### Explanation of different `<identifiers>`

- **`<namespace>`** is a subfolder, which prevents naming conflicts in case people come up with the same lattice file name. All contributors of lattice-summaries repo will have their own namespace and have to make sure that all **`<family>`** names are unique within their namespace. Note that the **`<namespace>`** can be different from the author(s) of a lattice. The actual author(s) of a lattice are listed in [`info.toml`](#infotoml) file and are also included as comment at the top of automatically generated lattice files. I decided it this way, because we may want to include lattices from other facilities. If Paul would upload the SLS2 lattice, the name would be something like `goslawski / sls2_design_v_std-user` even though Paul is not the author of the SLS2 lattice. The same would be true for a LOCO-measured BESSY II lattice file. In case a lattice **`<family>`** is maintained by multiple people an acronym like `gaa` for `Goslawski`, `Abo-Bakr` and `Andreas` would also be fine.
- **`<machine>`** Name of the machine (e.g. `bessy2`, `bessy3`, `mls`, `mls2`)
- **`<familiy>`** The goal of a **`<family>`** identifier is to make different versions of a lattice easier to compare on the lattice-summaries website. The name of the lattice *family* must be unique within *YOUR* **`<namespace>`**. Lattices within a family should belong logically together. For example Paul created several MLS2 lattices based on a scaled down version of BESSY II. In this case the family name should be something like `scaled-bessy2`. As during the B3 development presumably many lattices will be called `5ba-20p`, you could also choose a more memorable name like `jupiter`, `bravo` or `falcon`, which will make it easy to refer to a specific lattice during discussions.
- **`<version>`** The version name uniquely identifies a lattice within a **`<family>`**. It can be a simple number like `1` or `2` or a more descriptive name like `std-user`, `low-alpha` or `reference`. To please Paul it is also possible to use something like `1200mev-8p-2ba-new-wp-x909125-y909125`.

### Recommendations

Even there are no technical limitation, I would recommend to stick with lowercase characters and avoid using the `~` and `.` characters. This will make it easier on the command line and also provides some consistency. There recommended character to use are therefore `a-z0-9-`.

### Example names

* `kuske/bessy3_5ba-20p_v_reference`
* `kuske/bessy3_5ba-20p_v_long-bend-tgrb`
* `abo-bakr/bessy3_jupiter_v_2`
* `goslawski/mls2_scaled-bessy2_v_100m-1200mev-8p-2ba-new-wp-x909125-y909125`
* `mertens/bessy2_loco_v_std-user-2020-08-10`
* `andreas/bessy2_q5t2-off_v_4`
