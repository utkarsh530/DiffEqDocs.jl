# DiffEqBiological.jl API
```@meta
CurrentModule = DiffEqBiological
```

## Reaction Network Generation Macros
DiffEqBiological has three macros for generating reaction networks.
`@reaction_network` generates a complete network, including everything needed to
construct ODE, SDE and Jump problems directly from the network. For small
systems it is the recommended form to use.

`@min_reaction_network` constructs a network that just stores the basic
information needed to represent the species, parameters and chemical reactions.
This is sufficient for network analysis, such as calculating dependency graphs,
but means the network must be extended to build mathematical models, see
[`addodes!`](@ref), [`addsdes!`](@ref), and [`addjumps!`](@ref). 

`@empty_reaction_network` constructs an empty network. Both
`min_reaction_network`s and `empty_reaction_network`s can be enlarged using
[`addspecies!`](@ref), [`addparam!`](@ref), and [`addreaction!`](@ref). Once the
final chemistry for the network is set, [`addodes!`](@ref), [`addsdes!`](@ref),
and [`addjumps!`](@ref) can be called to build corresponding mathematical
models.

It is important to note for `@reaction_network` and `@min_reaction_network` that
species which are used *within the macro* as part of a rate expression, but not
as a substrate or product of some reaction, are not recognized as either a
species or parameter. i.e. avoid
```julia
rn = @reaction_network begin
    k*X, Y --> W
end k
```
as here `X` is never defined as either a species or parameter. This leads to
internal problems in the representation of reactions that *can not* be corrected
by subsequently calling [`addspecies!`](@ref). 

```@docs
@reaction_network
@min_reaction_network
@empty_reaction_network
```

## Basic properties
```@docs
species
speciesmap
params
paramsmap 
numspecies 
numparams
numreactions
```
## Reaction Properties
```@docs
substrates
products
dependents
dependants
ismassaction
substratestoich
substratesymstoich
productstoich
productsymstoich
netstoich
```

## Functions to Add Species, Parameters and Reactions to a Network
Both `@min_reaction_network` and `@empty_reaction_network` can be extended
with additional species, parameters, and reactions. 

*Note*, always add all species and parameter definitions before adding any
reaction definitions. Other orderings may result in incorrect information stored
within the generated network.

```@docs
addspecies!
addparam!
addreaction!
add_scale_noise_param!
```

## Functions to Add ODEs, SDEs or Jumps to a Network
```@docs
addodes!
addsdes!
addjumps!
```

## Generated Functions for Models
```@docs
oderhsfun
jacfun
paramjacfun
odefun
noisefun
sdefun
jumps
regularjumps
```

## Generated Expressions
```@docs
odeexprs
jacobianexprs
noiseexprs
jumpexprs
rateexpr
oderatelawexpr
ssaratelawexpr
```

## Network Comparison Functions
```@docs
==(rn1::DiffEqBase.AbstractReactionNetwork, rn2::DiffEqBase.AbstractReactionNetwork)
```

## Dependency Graphs
```@docs
rxtospecies_depgraph
speciestorx_depgraph
rxtorx_depgraph
```