---
layout: post
title: "Rules of Calculation in Quantum Electrodynamics (QED)"
date: 2025-06-07 13:52:18 +0200
categories:
  - Physics
  - Quantum Electrodynamics
tags:
  - QED
  - Photons
  - Quantum Mechanics
  - Feynman
---

This post provides an English summary of the concepts discussed on the page: [http://qed.ben.cz/pravidla-vypoctu](http://qed.ben.cz/pravidla-vypoctu). 

The original article delves into the fundamental rules governing calculations within Quantum Electrodynamics (QED).

**Core Principles and Calculation Methodology in QED:**

1.  **The Nature of Light and Photons:**
    In QED, light is understood as being carried by discrete packets of energy known as photons. These photons are the quanta, or smallest indivisible units, of the electromagnetic field. A key characteristic is that the energy of a photon is directly proportional to the frequency of the light wave it constitutes. This establishes the particle-wave duality that is central to quantum mechanics.

2.  **Representing Light Waves with Rotating Vectors (Phasors):**
    Light waves, which are typically described by sinusoidal functions, are represented in QED calculations as rotating radius vectors in a complex plane. This visual and mathematical representation is often referred to as a phasor or a probability amplitude vector.
    * The **length (magnitude)** of this radius vector corresponds to the amplitude of the light wave. Its square gives the probability of an event.
    * The **angle (phase)** of the rotating vector signifies the phase of the light wave at a given point in time or space. The rotation allows for a dynamic representation of the wave's progression through space and time.

3.  **Combining Light Waves and Probability Amplitudes:**
    The method of combining these vector representations depends on how the light waves or quantum events interact:
    * **Parallel Combination (Interference):** When multiple possibilities exist for a photon to reach a destination, or when light waves combine from different paths (e.g., in interference experiments), their corresponding radius vectors (probability amplitudes) are **summed**. This vector sum determines the resultant amplitude and phase, which then dictates the final probability of detection, explaining phenomena like constructive and destructive interference patterns. This is often phrased as "add amplitudes for indistinguishable alternatives."

    * **Series Combination (Propagation and Sequential Events):** When a photon undergoes a *sequence* of events (e.g., propagating from one point to another, then interacting with a particle, then propagating further), the probability amplitude for the entire sequence of events is found by **multiplying the probability amplitudes** for each individual step in that sequence. In QED's mathematical framework, these amplitudes are often expressed as complex exponential functions. Therefore, multiplying these exponential functions effectively combines the individual phase and magnitude contributions from each sequential stage of the photon's journey. This is analogous to how probabilities multiply for independent events in classical probability theory; however, in quantum mechanics, it's the *amplitudes* that multiply. Only the square of the final combined amplitude gives the observable probability of the entire sequence occurring. This aligns with the principle that "multiply amplitudes for sequential events."

4.  **The "Sum Over Histories" Principle (Feynman's Path Integral):**
    A cornerstone of QED, especially popularized by Richard Feynman, is the idea that the exact path a photon takes from its source to its target is not predetermined. Instead, all possible paths, no matter how improbable or convoluted, must be considered.
    * The calculation doesn't aim to find *the* path, but rather to determine the **total probability amplitude** for a photon to reach a specific point (e.g., a detector) by summing up contributions from *every single possible trajectory*. Each path contributes a certain amplitude (represented by a rotating vector) and phase to the total. This means even seemingly absurd paths, like a photon traveling to the moon and back to reach your eye, are theoretically included, albeit with a minuscule contribution.

5.  **Six Specific Rules for QED Calculations:**
    The article outlines a set of six fundamental rules that guide these complex calculations:
    * **Rule 1: Photon Representation:** Photons (and other particles like electrons) are represented by radius vectors (phasors) with a specific length and angle, which encode their probability amplitude.
    * **Rule 2: Probability from Vector Length:** The probability of a particular event occurring is directly derived from the square of the length (magnitude) of the associated radius vector. A longer vector implies a higher probability for that outcome.
    * **Rule 3: Summation for Detection Probability:** To find the total probability of a photon being detected at a specific point, one must sum the radius vectors corresponding to *all* possible, indistinguishable paths the photon could have taken to reach that detector. The length of the resulting summed vector then determines the final observable probability.
    * *(The original source implies further rules, likely detailing specific interaction vertices or propagators, but without more explicit detail from the original text, these three are the core tenets that can be elaborated upon from the provided summary.)*

This rigorous mathematical framework allows QED to calculate scattering cross-sections, magnetic moments of particles, and other properties with astonishing accuracy, making it one of the most successful and precisely tested theories in all of physics.

