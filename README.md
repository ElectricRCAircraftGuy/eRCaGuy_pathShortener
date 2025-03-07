

# Status

Done and works!

**Sponsor me for more:  https://github.com/sponsors/ElectricRCAircraftGuy**


# License

GNU GPLv3 or later. See: [LICENSE](LICENSE).


# eRCaGuy_PathShortener

A tool to fix and shorten paths on Linux (4096 chars max path length) &amp; Mac so that they can be unzipped, used, copied, etc. on Windows which has an insanely small max path length of \~256 chars.

This allows you to easily send documents and zipped up folders from your Linux computer to Windows friends, clients, or workers without having to worry about the path length being too long for them to unzip or use the files, or the files having illegal chars or symlinks in them. 

`path_shortener my_dir` does the following:

1. Copies the contents of `my_dir` into `my_dir_short`, so that it does *not* modify your original files.
1. Removes illegal Windows characters from paths, including: `<>:"\|?*`.
1. Shortens all paths to a length that is acceptable on Windows, as specified by you inside of `config.py`.
1. Copies symlinks as files, so they are not broken on Windows.

_As a safety rule, back up your original directory into a new location before running this program. I am not responsible for data loss._


# Recommended steps to send files from Linux to Windows

1. Fix broken symlinks using my [`fix_broken_symlinks`](https://github.com/ElectricRCAircraftGuy/eRCaGuy_dotfiles/blob/master/useful_scripts/fix_broken_symlinks.sh) tool from my [eRCaGuy_dotfiles](https://github.com/ElectricRCAircraftGuy/eRCaGuy_dotfiles) repo:
    ```bash
    # see help menu
    fix_broken_symlinks -h

    # Recursively find all broken symlinks in the current directory (.)
    fix_broken_symlinks

    # (Dry run) fix all broken symlinks in the current dir (.) by replacing 
    # their target paths with John's home dir instead of Gabriel's.
    fix_broken_symlinks . '/home/gabriel/some/dir' 'home/john/some/dir'
    
    # (Real run) fix all broken symlinks in the current dir (.) by replacing 
    # their target paths with John's home dir instead of Gabriel's. 
    # **Relative** symlinks will be created in the end.
    fix_broken_symlinks . '/home/gabriel/some/dir' 'home/john/some/dir' -f
    ```

1. Replace any other absolute symlinks with relative symlinks:
    ```bash
    # see the below commands in the help menu for fix_broken_symlinks
    fix_broken_symlinks -h

    # do a dry-run replacement of all absolute symlinks with relative symlinks
    symlinks -rsvt . | grep '^changed'

    # do the actual replacement of all absolute symlinks with relative symlinks
    symlinks -rsvc .
    ```

1. Run `path_shortener` on the directory you want to send to Windows:
    ```bash
    path_shortener path/to/your/directory
    ```

1. Zip up `path/to/your/directory_short` and send it over! You have now ensured that they have copies of good files instead of symlinks or broken symlinks, and that all paths are short enough and contain no illegal Windows characters.


# More details

Note that prior to installation (see installation instructions below), you must use `path/to/path_shortener.py` to run the program. If you are in the directory where it is located, use `./path_shortener.py`. After installation, you can use simply `path_shortener` to run the program.

```bash
# help menu
path_shortener -h

# shorten all paths in directory `test_paths`
path_shortener path/to/test_paths

# Same as above, but also open `meld` to compare the original and shortened paths
# when done. When meld opens, click "Keep highlighting" at the top to see the 
# differences between the two directories. Close it when done. 
path_shortener -m path/to/test_paths
```

If you run the above command, it will:
1. Copy `path/to/test_paths` to a new directory called `test_paths_short`, so that it does *not* modify your original files. This also removes symlinks. 
1. It will then remove illegal characters, replacing them with `_` and adding a `#ABCD`-style hash to the end of the filename to indicate it has been _fixed_. 
1. It will shorten all paths as necessary, adding a `@ABCD`-style hash to the end of the filename to indicate it has been shortened.
1. Lastly, it will autogenerate "namefiles" for all modifies paths, in which it will store the original file or folder name. 

    Ex: if directory `test_paths/sun_delta_crash_dog_delta_green_iota_sky` gets shortened to `test_paths_short/sun_d@7732`, then a namefile will be generated here: 

    1. `test_paths_short/!sun_d@7732_NAME.txt`
    1. `test_paths_short/sun_d@7732/!!sun_d@7732_NAME.txt`

    On Windows, the `!` char sorts to the top in the File Explorer, and `!!` sorts even higher than that, making namefiles for directories sort to the top of the file list so they are easy to find. 

    In the above example, both of the namefiles above for the single directory `sun_d@7732` will contain:
    ```
    Original directory name:
    sun_delta_crash_dog_delta_green_iota_sky
    ```

    For a file named `nu_crash_upsilon>.txt` which is fixed to `nu_crash_upsilon_#E209.txt`, the namefile will be called `nu_crash_upsilon_#E209_NAME.txt` and will contain:
    ```
    Original file name:
    nu_crash_upsilon>.txt
    ```

    Notice that in this case the problem was that the filename contained the illegal Windows char `>`. 

After running the program, inspect the `*_short/.eRCaGuy_PathShortener` directory to see various useful autogenerated files. 


# Meld path comparison before and after

Run the program with `-m` or `--meld` to automatically get this before and after path view when done. Be sure to click the "Keep highlighting" button at the top of the `meld` window when it comes up:

<p align="left" width="100%">
    <a href="images/meld_path_comparison_before_and_after.jpg" target="_blank">
        <img width="65%" src="images/meld_path_comparison_before_and_after.jpg"> 
    </a>
</p>


# Installation & example usage

_Tested on Linux Ubuntu 22.04._

1. Install dependencies:
    Python modules:

    ```bash
    pip3 install -r requirements.txt
    ```

    Meld: https://meldmerge.org/
    ```bash
    # On Linux Ubuntu
    sudo apt update
    sudo apt install meld
    ```

1. Download and install this program
    ```bash
    mkdir -p ~/Downloads/Install_Files
    cd ~/Downloads/Install_Files
    git clone https://github.com/ElectricRCAircraftGuy/eRCaGuy_PathShortener.git
    cd eRCaGuy_PathShortener
    ./INSTALL.sh
    ```

1. Open `config.py` and edit its settings if needed. 

1. Run the program:
    ```bash
    # Help menu
    path_shortener -h

    # shorten all paths in directory `test_paths`
    path_shortener path/to/test_paths
    ```

1. After running the program, inspect the `test_paths_short/.eRCaGuy_PathShortener` directory to see various useful autogenerated files. 

    For example, inside of `before_and_after_paths.txt`, you will find the following:

    ```
    BEFORE fixing and shortening paths:
    Path stats:
      max_allowed_path_len: 138
      max_len: 372
      total_path_count: 64
      too_long_path_count: 42
      symlink_path_count: 8
      illegal_windows_char_path_count: 34
      paths_to_fix_count: 50

    AFTER fixing and shortening paths:
    Path stats:
      max_allowed_path_len: 148
      max_len: 148
      total_path_count: 115
      too_long_path_count: 0
      symlink_path_count: 0
      illegal_windows_char_path_count: 0
      paths_to_fix_count: 0

    Path fixing and shortening has been successful!
    All paths are now fixed for Windows (illegal chars removed, no symlinks, and short enough).

    More length stats:
      Max allowed path len:   148 chars
      Max len BEFORE:         372 + 10 = 382
      Max len AFTER:          148
      Max namefile len AFTER: 148

    Before and after paths:
    Index:        Len: Original path
       ->         Len: Shortened path
       namefile:  Len: Longest namefile path, OR the same as the "shortened path" if there is no namefile

       0:         382: test_paths_short/sun_delta_crash_dog_delta_green_iota_sky/brown_forest/whisper_brown_high_wind_wind/fox_phi_lambda_crash_pi_kappa_fox/dog_ocean_sky_nu_chi_nu_mu_sigma_over_shines/beta_nu_over_psi/pi_<>:"\|?*_blue_nu_trees_mu_green_<>:"\|?*_lambda_over/brown_crash_tau_omega_crash_brown/dog_over_brown_aaaaaaaaaaaaaaaaaaaaaaabbbbbbbbbbbbbbbbbbbbbbbbbccccccccccccccccccccc/hel\o/c
       ->         129: test_paths_short/sun_d@7732/brown@BCFE/whisp@F3B2/fox_p@29D5/dog_o@C3E1/beta_@3187/pi___@1BA3/brow@ECD9/dog_@8400/hel_@3256/c
       namefile:  129: test_paths_short/sun_d@7732/brown@BCFE/whisp@F3B2/fox_p@29D5/dog_o@C3E1/beta_@3187/pi___@1BA3/brow@ECD9/dog_@8400/hel_@3256/c

       1:         382: test_paths_short/sun_delta_crash_dog_delta_green_iota_sky/brown_forest/whisper_brown_high_wind_wind/fox_phi_lambda_crash_pi_kappa_fox/dog_ocean_sky_nu_chi_nu_mu_sigma_over_shines/beta_nu_over_psi/pi_<>:"\|?*_blue_nu_trees_mu_green_<>:"\|?*_lambda_over/brown_crash_tau_omega_crash_brown/dog_over_brown_aaaaaaaaaaaaaaaaaaaaaaabbbbbbbbbbbbbbbbbbbbbbbbbccccccccccccccccccccc/hello/b
       ->         125: test_paths_short/sun_d@7732/brown@BCFE/whisp@F3B2/fox_p@29D5/dog_o@C3E1/beta_@3187/pi___@1BA3/brow@ECD9/dog_@8400/hello/b
       namefile:  125: test_paths_short/sun_d@7732/brown@BCFE/whisp@F3B2/fox_p@29D5/dog_o@C3E1/beta_@3187/pi___@1BA3/brow@ECD9/dog_@8400/hello/b

       2:         380: test_paths_short/sun_delta_crash_dog_delta_green_iota_sky/brown_forest/whisper_brown_high_wind_wind/fox_phi_lambda_crash_pi_kappa_fox/dog_ocean_sky_nu_chi_nu_mu_sigma_over_shines/beta_nu_over_psi/pi_<>:"\|?*_blue_nu_trees_mu_green_<>:"\|?*_lambda_over/brown_crash_tau_omega_crash_brown/dog_over_brown_aaaaaaaaaaaaaaaaaaaaaaabbbbbbbbbbbbbbbbbbbbbbbbbccccccccccccccccccccc/a.txt
       ->         123: test_paths_short/sun_d@7732/brown@BCFE/whisp@F3B2/fox_p@29D5/dog_o@C3E1/beta_@3187/pi___@1BA3/brow@ECD9/dog_@8400/a.txt
       namefile:  123: test_paths_short/sun_d@7732/brown@BCFE/whisp@F3B2/fox_p@29D5/dog_o@C3E1/beta_@3187/pi___@1BA3/brow@ECD9/dog_@8400/a.txt
    ```


# Other included programs

Other useful programs or submodules I wrote herein include:

1. `path_shortener_demo.sh`:
    ```bash
    # Run the demo on the demo `test_paths` directory
    ./path_shortener_demo.sh
    ```

1. `generate_test_paths.py`:
    ```bash
    # Regenerate a new `test_paths` directory with new semi-random paths to test
    ./generate_test_paths.py
    ```

1. `ansi_colors.py` module - allows you to print in colors. Ex: `print_red()`. 

1. `Tee.py` module - allows you to really easily "tee" your prints to both the console (stdout) and to one or more log files. 


# Design notes and TODOs

See: [design_notes_and_todos.md](design_notes_and_todos.md)
