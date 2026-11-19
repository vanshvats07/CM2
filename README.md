# Plant Growth Predictor using Newton Forward Interpolation

A beginner-friendly Computational Methods practical project that estimates a plant's height from equally spaced measurements. The browser handles the form and results display, **C++ performs the numerical calculation**, and Python with Matplotlib creates the graph. A small Python standard-library server connects them on your computer.

## Project files

- `main.cpp` — input validation, forward difference table, and Newton forward calculation.
- `index.html`, `style.css`, `script.js` — responsive botanical-themed page and form interactions.
- `server.py` — local web server and bridge to the compiled C++ engine and graph script.
- `plot.py` — Matplotlib graph generator.
- `plant_data.csv` — sample observations and prediction day; also used for data exchange.

## Requirements

- A C++17 compiler (`g++`, including Apple Clang on macOS).
- Python 3.
- Matplotlib (`python3 -m pip install matplotlib` if it is not already installed).

## Run locally

1. Open a terminal in this project folder.
2. Start the server:

   ```bash
   python3 server.py
   ```

   The server compiles `main.cpp` to `plant_predictor` when needed. To compile manually instead, run `g++ -std=c++17 -Wall -Wextra -pedantic main.cpp -o plant_predictor`.
3. Visit **http://127.0.0.1:8000** in your browser. The sample values load automatically and the page calculates the sample prediction.
4. Change the observation rows or prediction day and choose **Predict growth**. The C++ program validates the data and returns the result and difference table; Python refreshes the graph.
5. Press **Ctrl+C** in the terminal to stop the server.

The server binds only to `127.0.0.1` and is intended for local use. Each prediction updates `plant_data.csv` with the submitted values.

## Sample input and output

The supplied `plant_data.csv` contains:

| Day | Height (cm) |
|---:|---:|
| 0 | 2 |
| 5 | 4 |
| 10 | 7 |
| 15 | 11 |
| 20 | 16 |

Prediction day: **25** (extrapolation, since it is beyond the last observation).

| Day | Δ⁰y | Δy | Δ²y | Δ³y | Δ⁴y |
|---:|---:|---:|---:|---:|---:|
| 0 | 2 | 2 | 1 | 0 | 0 |
| 5 | 4 | 3 | 1 | 0 | |
| 10 | 7 | 4 | 1 | | |
| 15 | 11 | 5 | | | |
| 20 | 16 | | | | |

Here, the interval is `h = 5` days and `u = (25 - 0) / 5 = 5`. The predicted height is **22 cm**. The graph marks this estimated point separately from the measured observations.

You can run the numerical engine directly with `./plant_predictor plant_data.csv` after compiling. It prints a JSON result, including the prediction, mode, and forward difference columns. Invalid values, out-of-order days, and unequal day intervals are reported as errors.

## Method and algorithm

The Newton forward formula is:

```text
y(x) = y0 + u Δy0 + u(u - 1)/2! Δ²y0
         + u(u - 1)(u - 2)/3! Δ³y0 + ...
u = (x - x0) / h
```

1. Read the observations and requested day; require at least two finite measurements.
2. Check that the days increase with a constant interval `h`.
3. Repeatedly subtract neighbouring values to build the forward difference columns.
4. Calculate `u`, then add each leading difference multiplied by its Newton formula factor.
5. Label the result interpolation when the requested day is within the observed range, or extrapolation otherwise.

Forward interpolation is most directly suited to points near the beginning of equally spaced data. Extrapolation is less reliable: the result continues the mathematical trend in the observations and is not a guarantee of how a living plant will grow.

## Short viva explanation

**What does the project do?** It estimates plant height at a requested day from earlier, equally spaced height measurements.

**What is a forward difference?** It is the difference between adjacent values in a column. Repeating this operation produces higher-order differences used by the interpolation polynomial.

**Why calculate `u`?** It expresses the requested day's distance from the first observation in units of the common interval.

**What happens in this sample?** The five-day observations produce first differences `2, 3, 4, 5` and second differences `1, 1, 1`. For day 25, `u = 5` and the formula gives 22 cm. Since day 25 is after the last recorded day (20), this is extrapolation.

**What is the botanical application?** It demonstrates how recorded plant growth can be used to estimate height between measurements or tentatively extend a growth trend, while real plants may respond to conditions not represented in the data.
# CM2
