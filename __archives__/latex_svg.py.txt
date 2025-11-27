import os
import shutil
import subprocess
import tempfile
from typing import Optional

def _which(cmd: str) -> Optional[str]:
    return shutil.which(cmd)

def have_latex_toolchain() -> bool:
    return (_which("latex") and _which("dvisvgm")) or (_which("pdflatex") and _which("dvisvgm"))

def latex_to_svg_fragment(latex_code: str, scale: float = 1.0) -> Optional[str]:
    use_latex = _which("latex") is not None
    use_pdflatex = _which("pdflatex") is not None
    if not _which("dvisvgm") or not (use_latex or use_pdflatex):
        print("[latex_svg] Toolchain LaTeX manquante (latex/pdflatex et/ou dvisvgm).")
        return None

    tex_template = r"""
\documentclass[preview,border=2pt]{standalone}
\usepackage[T1]{fontenc}
\usepackage{amsmath,amssymb}
\begin{document}
%s
\end{document}
"""
    src = tex_template % latex_code

    with tempfile.TemporaryDirectory() as tmp:
        tex_path = os.path.join(tmp, "snippet.tex")
        with open(tex_path, "w", encoding="utf-8") as f:
            f.write(src)

        out_svg = os.path.join(tmp, "snippet.svg")

        if use_latex:
            run1 = subprocess.run(
                ["latex", "-interaction=nonstopmode", "snippet.tex"],
                cwd=tmp, stdout=subprocess.PIPE, stderr=subprocess.STDOUT, text=True
            )
            if run1.returncode != 0:
                print("[latex_svg] latex a échoué:\n", run1.stdout[:800])
                if not use_pdflatex:
                    return None
            else:
                #args = ["dvisvgm", "snippet.dvi", "-n", "--exact", f"--scale={scale}", "-o=snippet.svg"]
                args = ["dvisvgm", "snippet.dvi", "-n", "--exact", f"--scale={scale}", "-o", "snippet.svg"]

                run2 = subprocess.run(args, cwd=tmp, stdout=subprocess.PIPE, stderr=subprocess.STDOUT, text=True)
                if run2.returncode != 0 or not os.path.exists(out_svg):
                    print("[latex_svg] dvisvgm(DVI) a échoué:\n", run2.stdout[:800])
                    return None
                return open(out_svg, "r", encoding="utf-8").read()

        runp = subprocess.run(
            ["pdflatex", "-interaction=nonstopmode", "snippet.tex"],
            cwd=tmp, stdout=subprocess.PIPE, stderr=subprocess.STDOUT, text=True
        )
        if runp.returncode != 0:
            print("[latex_svg] pdflatex a échoué:\n", runp.stdout[:800])
            return None

        #args = ["dvisvgm", "--pdf", "snippet.pdf", "-n", "--exact", f"--scale={scale}", "-o=snippet.svg"]
        args = ["dvisvgm", "--pdf", "snippet.pdf", "-n", "--exact", f"--scale={scale}", "-o", "snippet.svg"]


        run2 = subprocess.run(args, cwd=tmp, stdout=subprocess.PIPE, stderr=subprocess.STDOUT, text=True)
        if run2.returncode != 0 or not os.path.exists(out_svg):
            print("[latex_svg] dvisvgm(PDF) a échoué:\n", run2.stdout[:800])
            return None

        return open(out_svg, "r", encoding="utf-8").read()
