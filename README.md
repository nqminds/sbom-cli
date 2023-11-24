# Vulnerability analyses tools

1. ### Please download the vulnerability-reports folder and place it in the vulnerability-analisis-tools module:

   - vulnerability-reports directory can be downloaded from [here](https://drive.google.com/file/d/1ZV302sOZXYu7JUiM5fVgrMi3lYxGw1VH/view?usp=drive_link). This also contains all the (National Vulnerability Database)[NVD](https://nvd.nist.gov/) CPE/CVE/CWE data. (recommended)

---

2. ### Clone the repository

```sh
git clone https://github.com/nqminds/cyber.git
```

---

3. ### Ensure node and npm are installed.

```sh
# please use node v16
node --version
V16.16.0

# recommended
npm --version
8.11.0
```

4. ### Install [docker](https://www.docker.com/get-started/) and [docker-compose](https://docs.docker.com/compose/install/) on your machine

---

5. ### Add current user to the docker group

```sh
sudo usermod -aG docker $USER
```

---

6. ### Install all dependencies and pull the necessary docker images

```sh
npm install
```

---

7. ###  Create a global symlink to nqmvul tool

```sh
npm link
```

7. ### Download the Git Advisory Database from [here](https://github.com/github/advisory-database) and replace this path in ./config/config.json with your local advisory-database/advisories.

```json
{
  "gitAdvisoryDbPath": "/path/to/advisory-database/advisories"
}
```

The above is necessary only for getting information about GHSA vulnerability codes. No need to download this data in order to generate an SBOM.

---

8. ### For speeding NIST API requsts an API key cen be obtained from [here](https://nvd.nist.gov/developers/request-an-api-key). Please create a cyber/.env file in the root directory cyber/ and add the key as:

```yaml
NIST_API_KEY=your_NIST_api_key;
```

---

9. ### To improve the classification of weaknesses, include an OpenAi API key to the global cyber/.env. An API key can be obtain from [here](https://platform.openai.com/api-keys):

```yaml
OPENAI_API_KEY=your_OpenAi_api_key
```
---

11. ### To generate a Software Bill Of Materials (SBOM) for the ecosystems bellow use the command (Uses [syft](https://github.com/anchore/syft) and [grype](https://github.com/anchore/grype)):

```sh
nqmvul -generateSbom <project_path> <project_name>
```

- Alpine (apk)
- C (conan)
- C++ (conan)
- Dart (pubs)
- Debian (dpkg)
- Dotnet (deps.json)
- Objective-C (cocoapods)
- Elixir (mix)
- Erlang (rebar3)
- Go (go.mod, Go binaries)
- Haskell (cabal, stack)
- Java (jar, ear, war, par, sar, nar, native-image)
- JavaScript (npm, yarn)
- Jenkins Plugins (jpi, hpi)
- Linux kernel archives (vmlinz)
- Linux kernel modules (ko)
- Nix (outputs in /nix/store)
- PHP (composer)
- Python (wheel, egg, poetry, requirements.txt)
- Red Hat (rpm)
- Ruby (gem)
- Rust (cargo.lock)
- Swift (cocoapods, swift-package-manager)

---

12. ### To generate a Software Bill Of Materials (SBOM) for other C/C++ ecosystems (Uses [ccscanner](https://github.com/lkpsg/ccscanner) and [grype](https://github.com/anchore/grype)).

```sh
nqmvul -generateCCPPReport <path_to_c/cpp_project> <project_name>
```

## Package Managers:

- Deb
- Conan
- Vcpkg
- Clib
- CPM
- Buckaroo
- Dds
- Hunter
- Cppget
- Xrepo
- Gitsubmodule
- Pkg-config

# Build Systems

- Make
- CMake
- Autoconf
- Bazel
- Meson
- MSBuild
- Xmake
- Build2
- Buck

---

13. ### After generating a Software Bill Of Material (SBOM) use the visualization tool for a more detailed representation. <span style="color: yellow;">Only CycloneDx json format is supported.</span>
    ![Example Image](./images/app.png)

---

### Command Line Tool Usage

```sh
nqmvul -help;
```

Presents the list of commands

```sh
nqmvul -getCpes <path_to_sbom.json>
```

The -getCpes flag will parse an SBOM and return a list of CPEs in the 2.3 format.

```sh
nqmvul -listCpeDetails <path_to_sbom.json>
```

The -listCpeDetails flag will parse an SBOM and return detailed information about each CPE, such as CVEs and CWEs

```sh
nqmvul -getCves <CPE>
```

The -listCVEs will return the known CVEs and CWS for a CPE. The CPE must be in CPE2.3 format e.g. "cpe:2.3:a:busybox:busybox:1.33.2"

```sh
nqmvul -writeCves <path_to_sbom.json> <path_to_output_directory>
```

The -writeCVEs flag will write all the CVE data of an sbom into a json format to output_directory/cveData.json

```sh
nqmvul -getHistoricalCpes <CPE>
```

The -getHistoricalCpes flag will return all known versions of the input CPE. The CPE must be in CPE2.3 format e.g. "cpe:2.3:a:busybox:busybox:1.33.2"

```sh
nqmvul -getHistoricalCves <CVE>
```

The -getHistoricalCves flag will return all known versions of the input CVE. Supported CVE format: "CVE-2022-48174"

```sh
nqmvul -getCweInfo <CWE,CWE,...>
```

The -getCweInfo flag will return information such as description for each CWE. Can take one or more CWEs. If multiple CWEs are passed, they must be writen without any space e.g. 'CWE-476,CWE-681'

```sh
nqmvul -generateSbom <project_path> <project_name>
```

The -generateSbom flag will generate an SBOM and a vulnerability report for a project. SBOM is saved to /vulnerability-reports/sboms/project_name.json. Vulnerability report is saved to /vulnerability-reports/reports/vulnerability-report-project_name. Syft and Grype must be installed to run this command

```sh
nqmvul -listVulnerabilities <path_to_vulnerability_report>
```

The -listVunlerabilities flag will list all vulnerabilities previously detected by [grype](https://github.com/anchore/grype/blob/main/README.md)

```sh
nqmvul -genDependencies <cpp_project> <project_name>
```

- The first argument should represent the path to any C/C++ project you wish to scan.
- The project_name is utilised to save the extracted dependency list as /vulnerability-reports/ccsDependencies/project_name_dependencies.

```sh
nqmvul -generateConan <project_name>
```

The -generateConan flag will generate a conanfile.txt for that project. ccsDependencies/project_name_dependencies must exist before running this command. The dependency file is created by using the `-genDep` flag.

```sh
nqmvul -mapCpes <project_name>
```

The -mapCpes will create a list of known cpes for each dependency in /vulnerability-reports/ccsDependencies/project_name_dependencies and save them to vulnerability-reports/cpes/cpeMapping.json.

```sh
nqmvul -generateCSbom <project_name> <sbom_type>
```

The -generateCSbom flag can be used to generate an SBOM only for C/CPP projects that are not supported by syft, e.g. C/CPP projects that are not using the CONAN package manager. It takes two arguments, the project name and format(only json or xml). Please ensure that /vulnerability-reports/conan-files/<project_name>/conanfile.txt and /vulnerability-reports/cpe_data.csv exists before running the command.

```sh
nqmvul -getGhsa <GHSA-vulnerability>
```

The -getGhsa flag will return detailed information about a known GHSA vulnerability. Please ensure the GHSA vul is valid and matches the following format e.g GHSA-j8xg-fqg3-53r7.

```sh
nqmvul -extractGhsas <path_to_vulnerability_report>
```

The -extractGhsas will return an array of GHSA codes. Before running this command please replace the `gitAdvisoryDbPath` path from `config.json` with your local advisory-database/advisories path.

```sh
nqmvul -classifyCwe <CWE-ID>
```

The -classifyCwe flag will try and classify the CWE_ID as one of the following types: not-memory-related, other-memory-related, spatial-memory-related, temporal-memory-related. Please ensure the CWE_ID is valid and of the following form: e.g. 354. If the CWE_ID doesn't exist in the current database it will return "not found".

```sh
nqmvul -getHistory <CPE> -hist
```

The -getHistory flag coupled with -hist will return all `previous` versions of a CPE and for each version will try and find known CVEs and CWEs (vulnerabilities). It also classifies each CWE into memory related issues or other. Can take as an argument various types of CPEs such as: cpe:2.3:a:busybox:busybox:1.33.2, cpe:/a:doxygen:doxygen:1.7.2. For cpes that contain ':_' please place them inside quotes. e.g. : ` nqmvul -getHistory "cpe:2.3:a:openssl:openssl:1.1.1:_:_:_:_:_:_:_" -hist`. Output is saved to output/output.txt

```sh
nqmvul -getHistory <CPE> -all
```

The -getHistory flag coupled with -hist will return all `known` versions of a CPE and for each version will try and find known CVEs and CWEs (vulnerabilities). It also classifies each CWE into memory related issues or other. Can take as an argument various types of CPEs such as: cpe:2.3:a:busybox:busybox:1.33.2, cpe:/a:doxygen:doxygen:1.7.2. For cpes that contain ':_' please place them inside quotes. e.g. : ` nqmvul -getHistory "cpe:2.3:a:openssl:openssl:1.1.1:_:_:_:_:_:_:_" -hist`. Output is saved to output/output.txt

```sh
nqmvul -generateCCPPReport <path_to_c/cpp_project> <project_name>
```

The --generateCCPPReport can be used to scan any type of C/CPP project. Running this command will combine some of the previous tools in order to produce an sbom and a vulnerability report.
