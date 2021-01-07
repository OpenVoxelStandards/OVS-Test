# OVS-2 Stratum File Format

<style>
  .info td { text-align: left; }
  .info th { text-align: right; }
</style>

<table class="info">
  <tbody>
  <tr>
    <th>OVS:</th>
    <td>2</td>
  </tr>
  <tr>
    <th>Title:</th>
    <td>Stratum File Format</td>
  </tr>
  <tr>
    <th>Author:</th>
    <td>DocW &lt;docwhomc at gmail.com&gt;</td>
  </tr>
  <tr>
    <th>Version:</th>
    <td>_draft_</td>
  </tr>
  </tbody>
</table>

The Open Voxel Standards Stratum File Format (OVS SFF) encodes voxel
composition using the following sections:
 1. header
 2. material
    1. strata
    2. segments
    3. voxels
 3. supplemental data
    1. strata
    2. segments
    3. voxels
 4. footer
Voxel composition consists of a material and optional supplemental data.

## Header Section

The header section consists of:
 1. the <abbr title="Open Voxel Standards Stratum File Format">OVS
    SFF</abbr> magic number;
 2. the <abbr title="Open Voxel Standards Stratum File Format">OVS
    SFF</abbr> version according to which the file is encoded;
 3. the dimensions of the domain described by the file;
 4. the number of stratum, segment, and voxel entries in their
    respective subsections of the material and supplemental data
    sections; and
 5. the default material and supplemental data.


## Material and Supplemental Data Sections

In the material section `composition = material`, while in the
supplemental data section `composition = supplemental-data`.
To avoid needing to specify the composition of all voxels, a default
material and a default supplemental data shall be used for those voxel
whose respective attributes are not explicitly specified.

The `material` symbol represents a fixed or variable width encoding a
voxel material.
The exact correspondence between the encoded and decoded material forms
is beyond the scope of this standard as it is dependent on the materials
defined by the application.

The `supplemental-data` symbol represents a variable length encoding of
additional data (i.e., data other than material) regarding the
composition of voxels.
The format of the supplemental data itself is not specified by the
current draft.

### Strata Subsections

Strata subsections shall begin by specifying the format used via an big
endian, unsigned, two-bit integer value.
The options are:

 0. range strata format (`strata = range-strata`),
 1. continuum strata format (`strata = continuum-strata`), and
 2. implicit level strata format (`strata = implicit-level-strata`).

In the range strata format, each strata specification consists of a
z-range followed by the composition data of the section.

```
range-strata = { range-stratum } ;
```

```
range-stratum = z-range, composition ;
```

The value of `z-stop` shall always be greater than or equal to that of
`z-start`.

```
z-range = z-start, z-stop ;
```

The z-ranges of different strata may overlap&mdash;with the composition
of succeeding strata taking precedence over that of preceding
strata&mdash;subject to the following rules and restrictions:

 - For any two overlapping strata, the z-range of one stratum must be
   fully enclosed by the z-range of the other stratum, respectively, the
   enclosed stratum, included stratum, or inclusion and the enclosing
   stratum.
 - Strata shall be ordered by ascending z-value.
   As a consequence of the preceding requirement, strata cannot have the
   same z-start or z-stop values and inclusions must succeed the strata
   enclosing them.

The composition of succeeding strata shall take precedence over that of
preceding strata.

Since overlapping strata are inconsistent with the continuum and
implicit level strata formats, their strata shall simply be ordered by
ascending z-level.

```
continuum-strata = z-start, { continuum-stratum } ;
```

```
continuum-stratum = z-size, composition ;
```

In the implicit level strata format, each strata consists of a single
voxel high layer and the composition of each layer is explicitly
specified (i.e., no layers are skipped).
As a result, the z-values of those levels need not be specified
explicitly.

```
implicit-level-strata = { implicit-level-stratum } ;
```

```
implicit-level-stratum = { composition } ;
```

### Segments Subsections

```
segments = { segment } ;
```

```
segment = xyz-range, composition ;
```

```
xyz-range = xyz-start, xyz-stop ;
```

```
xyz-start = x-start, y-start, z-start
xyz-stop = x-stop, y-stop, z-stop ;
```

### Voxels Subsections

```
voxels = { voxel } ;
```

```
voxel = xyz, composition ;
```

```
xyz = x, y, z ;
```

## Footer Section

## Mathematical Definition

Let _S_ be the set of strata.

_&forall; s<sub>i</sub> &isin; S, &exist; z<sub>i,0</sub>,
z<sub>i,1</sub> &isin; &integers;_ s.t. _z<sub>i,0</sub> &le;
z<sub>i,1</sub>_ where _z<sub>i,0</sub>_ and _z<sub>i,1</sub>_ are
respectively the first and last z-levels of the stratum _s<sub>i</sub>_.

_&forall; s<sub>0</sub>, s<sub>1</sub> &isin; S_:

 - _s<sub>0</sub>_ and _s<sub>1</sub>_ are disjoint are disjoint
   <abbr title="if and only if">iff</abbr> _r<sub>0,1</sub> &lt;
   r<sub>1,0</sub> &or; r<sub>1,1</sub> &lt; r<sub>0,0</sub>_;
 - _s<sub>1</sub>_ is an inclusion in _s<sub>0</sub>_ iff
   _r<sub>0,0</sub> &lt; r<sub>1,0</sub> &and; r<sub>1,1</sub> &lt;
   r<sub>0,1</sub>_; and
 - _s<sub>0</sub>_ is an inclusion in _s<sub>1</sub>_ iff
   _(r<sub>1,0</sub> &lt; r<sub>0,0</sub> &and; r<sub>0,1</sub> &lt;
   r<sub>1,1</sub>)_.
