# Castile

Castile is a modern Elixir SOAP API client.
It borrows ideas heavily from [Detergent](https://github.com/devinus/detergent)/yaws.

Why write another SOAP client? We really wanted to use Detergent/Detergentex,
but ran into some issues (and also wanted to use HTTPoison as the HTTP client).

Detergent itself hasn't really been updated since 2013 (a time when erlang
itself had no maps even), and Detergentex being just a lightweight wrapper
around it, has the same shortcomings (even worse, the detergent version on
hex.pm that detergentex relies on has no support for HTTPS). Erlsom has also
been updated in the mean time, improving XML write speed and adding support for
binaries natively.

[Castile](https://en.wikipedia.org/wiki/Castile_soap) is an attempt at a fresh
start, with a small API that's easy to use in Elixir.

At the moment, the supported subset is SOAP 1.1 with WSDL 1.1 (and
document/literal format). SOAP 1.2 and WSDL 2.0 are on the roadmap, but none of
the APIs we interact with use either of those, so I'm having problems finding
a public API to test against (if you find one, please open an issue!).

## Installation

(At the moment, installation is possible only from git directly, as Castile uses
an erlsom version that's more recent than the hex.pm package. As such, I'm
unable to release an updated version on Hex until that's resolved.)


```elixir
def deps do
  [
    {:castile, git: "https://github.com/polyfox/castile"}
  ]
end
```

Docs can be found at [https://hexdocs.pm/castile](https://hexdocs.pm/castile).

## Usage

```elixir
# It's recommended to do init_model at compile time, as an @attr
@model = Castile.init_model("CountryInfoService.wsdl")

# we take maps as input and return binaries
{:ok, resp} = Castile.call(@model, :CountryISOCode, %{sCountryName: "Netherlands"})
# "NL"

# More complex results get cast into maps:
model = Castile.init_model("BLZService.wsdl")
{:ok, resp} = Castile.call(model, :getBank, %{blz: "70070010"})
# resp => %{
#   bezeichnung: "Deutsche Bank",
#   bic: "DEUTDEMMXXX",
#   ort: "München",
#   plz: "80271"
# }
```

# TODO

- [X] Faults (1.1)
- [ ] HTTP client as adapter (specify module)
- [ ] SOAP 1.2 support
- [ ] RPC/encoding RPC/literal style (multiple bodies)
- [ ] WSDL 2.0 support
- [ ] Attachments/multipart

# Thanks

- [detergent](https://github.com/devinus/detergent)/yaws for the original SOAP
    implementation.
- [bet365/soap](https://github.com/bet365/soap) for some of the ideas/fixes
    (simplifying the WSDL XSDs, already_imported schema patch)
