# spacedustrs

This is a rust API wrapper for https://spacetraders.io V2

Now generated by https://openapi-generator.tech/docs/generators/rust/

Includes patches for documentation missing from the main API docs

## Quickstart

Use the following example to get started.

```rust
use spacedust::apis::agents_api::get_my_agent;
use spacedust::apis::configuration::Configuration;
use spacedust::apis::default_api::register;
use spacedust::models::register_request::{Faction, RegisterRequest};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Create Configuration
    let mut conf = Configuration::new();

    // Create Register Request
    let reg_req = RegisterRequest::new(Faction::Cosmic, "<3-14 character string>".to_string());

    // Register Agent
    let register_response = register(&conf, Some(reg_req)).await;

    match register_response {
        Ok(res) => {
            println!("{:#?}", res);
            // Update Config with Agent Token
            conf.bearer_access_token = Some(res.data.token);
        }
        Err(err_res) => {
            panic!("{:#?}", err_res);
        }
    }

    // Get Agent Details to Confirm Working
    match get_my_agent(&conf).await {
        Ok(res) => {
            println!("{:#?}", res);
            // Print Symbol
            println!("My Symbol: {:#?}", res.data.symbol);
        }
        Err(err_res) => {
            panic!("{:#?}", err_res);
        }
    }

    Ok(())
}
```

## Generation Instructions

**FIRST** update the [docs fork with the spacedust patches](https://github.com/spacetraders-rs/api-docs-spacedust-patch). You can just 'Sync Fork' from the github web UI unless there are merge conflicts (there haven't been so far)

Clone the project, including --recurse-submodules:

```bash
git clone --recurse-submodules git@github.com:brct-james/spacedustrs.git
```

Ensure submodules are initialized and updated:

```bash
git submodule update --recursive
```

Clean the output directory:

```bash
sudo rm -rf client-dist
```

Remake the directory:

```bash
mkdir client-dist
```

Update your local image of openapitools/openapi-generator-cli:latest-release

```bash
docker pull openapitools/openapi-generator-cli:latest-release
```

Run the following command, which uses the openapi-generator-cli docker image to generate the client:

```bash
docker run --rm \
  -v ${PWD}:/local openapitools/openapi-generator-cli:latest-release generate \
  -i /local/spacetraders-api-docs/reference/SpaceTraders.json \
  -g rust \
  -o /local/client-dist \
  --additional-properties=packageName=spacedust,supportAsync=true,supportMiddleware=true
```

Copy the client-dist/src to src

```bash
sudo rm -rf src
mkdir src
cp -r client-dist/src .
```

Update Cargo.toml with any new dependencies, update documentation, tick version, commit changes, and publish to cargo
