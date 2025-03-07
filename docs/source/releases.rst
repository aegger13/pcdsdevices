Release History
###############


v4.2.0 (2021-03-03)
===================

Features
--------
- Happi IOC Data: added new EntryInfo to happi.containers.LCLSItem  for ioc
  configuration data including engineer, location, hutch, release, arch, name,
  and ioc type.
- New containers: added new Happi containers with device specific metadata for
  building MODS IOCs.
- Custom status print for `LODCM` object.
- Added the `MPOD` class that determines the appropriate MPOD Channel classes. This is to help support the happi entry creation from the questionnaire.
- Add custom status for LaserTiming and for PseudoSingleInterface
- Add verbose_name attribute to PseudoSingleInterface and caclulated dial position
- Add verbose_name property to LaserTiming

Device Updates
--------------
- `LODCM` object has been updated to contain the Energy motors as well as the other motors and offsets.
- Update various signal kinds on PTMPLC from omitted to normal or config as
  appropriate.
- ThorlabsWfs40: Added wavefront PV and viewer, added some docs

New Devices
-----------
- `OffsetMotor` - PseudoPositioner with an offset
- Add GHCPLC (Hot Cathode) class as a counterpart to the GCCPLC (Cold Cathode)
  class.

Bugfixes
--------
- Fix issue where the Kappa had an incorrect e_phi calculation
  in certain situations.
- Fix issue where the Kappa used the calculated motors for the
  safety check instead of the real motors.
- Fix issue where legacy attenuator classes would break bluesky scans.
- Fix Kappa behavior for kappa angles above 180 degrees.

Contributors
------------
- cristinasewell
- klauer
- slacAdpai
- slactjohnson
- zllentz


v4.1.0 (2021-02-10)
===================

API Changes
-----------
- Update twincat motors to use the correct homing PV.
  This is an alternative PV to the normal motor record PVs for IOC/PLC
  management reasons.
  It is possible that this will break devices that have not updated to the
  latest motion PLC library.
- Added ``format`` and ``scale`` arguments to
  :func:`~pcdsdevices.utils.get_status_float`, which affect floating point
  formatting of values available in the ``status_info`` dictionary.
- CVMI Motion System Prefix: 'TMO:CVMI'
- KTOF Motion System Prefix: 'TMO:KTOF'

Features
--------
- Added :func:`~pcdsdevices.utils.format_status_table` for ease of generating
  status tables from ``status_info`` dictionaries.
- Added :func:`~pcdsdevices.utils.combine_status_info` to simplify joining
  status information of child components.

Device Updates
--------------
- VCN upper limit can be changed from epics.
- Added the ``active`` component to
  :class:`~pcdsdevices.attenuator.AttenuatorCalculatorFilter`, indicating
  whether or not the filter should be used in calculations.
- Multiple devices have been modified to include explicit argument and keyword
  argument names in ``__init__`` for clarity and introspectability.

New Devices
-----------
- XYGridStage - maps targets from grids to x,y positions, and supports multiple samples on a stage.
- Added :class:`~pcdsdevices.attenuator.AT1K4` and supporting SXR solid
  attenuator classes, including
  :class:`~pcdsdevices.attenuator.AttenuatorCalculatorSXR_Blade`,
  :class:`~pcdsdevices.attenuator.AttenuatorCalculatorSXR_FourBlade`, and
  :class:`~pcdsdevices.attenuator.AttenuatorSXR_Ladder`.
- pcdsdevices.cvmi_motion.CVMI
- pcdsdevices.cvmi_motion.KTOF

Bugfixes
--------
- The transmission status value for the 3rd harmonic has been fixed, it was previously using the wrong value.

Maintenance
-----------
- The test suite will now find all devices in pcdsdevices submodules at
  arbitrary import depth.
- Minor cleanup of the pcds-tag conda recipe
- Relocate happi name length restriction for lcls devices to this package
  as a requirement on LCLSItem
- Updated AT2L0 to use newer status formatting utilities.
- Added prettytable as an explicit dependency.  It was previously assumed to
  be installed with a sub-dependency.
- Added test suite to try to instantiate all device classes with
  ``make_fake_device`` and perform status print formatting checks on them.
- Added ``include_plus_sign`` option for ``get_status_float``.
- Perform continuous integration tests with pip-based installs, with
  dependencies installed from PyPI.

Contributors
------------
- cristinasewell
- ghalym
- jsheppard95
- klauer
- zllentz


v4.0.0 (2020-12-22)
===================

API Changes
-----------
- On our EPICS motor classes, remove the ability to use setattr for
  `low_limit` and `high_limit`.
- SmarActOpenLoop: Combined scan_move_cmd and scan_pos into single EpicsSignal,
  scan_move, with separate read and write PVs.

Features
--------
- Added pseudo motors and related calculations to the `Kappa` object.
- Added two methods to `EpicsMotorInterface`: `set_high_limit()` and `set_low_limit()`, as well as `get_low_limit()` and `get_high_limit()`.
- Added a little method to clear limits: `clear_limits` - by EPICS convention, this sets both limits to 0.
- Added 3rd harmonic frequncy transmission info to the status print for the Attenuator.
- Added custom status print for `XOffsetMirror`, `OffsetMirror`, `KBOMirror`, and `FFMirror`.
- Add custom status print for `gon` classes: `BaseGon`, and `XYZStage` class.
- Add notepad signals to `LaserTiming` and `DelayBase` classes

Device Updates
--------------
- Instead of creating separated devices for Fundamental Frequency and 3rd Harmonic Frequency, we are now creating Attenuators that have both frequencies.
- EpicsMotorInterface: Add metadata to various upstream Ophyd methods to clean
  up screens generated via Typhos.
- Allow negative positions in `LaserTiming` and `LaserTimingCompensation`
  devices
- Add LED power to the Mono device.
- led metadata scalar range

New Devices
-----------
- Added `ExitSlits` device.

Bugfixes
--------
- sequencer.EventSequencer.EventSequence: Add an explicit put to SEQ.PROC to
  force the event sequencer to update with the new sequence.
- Fix position handling in `ReversedTimeToolDelay`
- AvgSignal will no longer spam exceptions text to the terminal when the signal
  it is averaging is disconnected. This will primarily be noticed in the
  BeamStats class, loaded in every hutch-python session.

Contributors
------------
- ZryletTC
- cristinasewell
- ghalym
- tjohnson
- zllentz


v3.3.0 (2020-11-17)
===================

API Changes
-----------
- The belens classes use ``pcdscalc`` to handle their calculations,
  changing the lens file specifications as follows:

  - Changed the ``read_lens`` to open a normal file instead of a ``.yaml``
    file, and to be able to read one lens set at the time from a file
    with multiple lens sets.
  - Changed the ``create_lens`` methods to use a normal file instead of
    ``.yaml`` file, and also to be able to create a set with multiple sets of lens.

- This is not expected to be breaking, as this feature
  is underused in the deployed environments.

Features
--------
- Added a ``LensStack.set_lens_set`` method to allow the user
  to choose what set from the file to use for calculations.
- Added a factory function ``acromag_ch_factory_func`` to
  support the creation of happi entries from the questionnaire
  for a single acromag channel.

  - Added an alias for this function ``AcromagChannel``.

- Added a custom status print for motors by overriding the status info handler.
- Added a new component for ``EpicsMotorInterface.dial_position``
- Added a new method ``EpicsMotorInterface.check_limit_switches`` to return a
  string visualization of the limit switch state.
- Added a custom status print for slits by overriding the status info handler.
- Added a helper function in ``utils.get_status_value`` to support getting
  a value from a dictionary.
- Added a custom status print for PIM by overriding the status info handler.
- Added a custom status print for IPM by overriding the status info handler.

Device Updates
--------------
- ``SmarActOpenLoop``: open loop steps signal changed to RO.
  Added some docs.
- ``PCDSAreaDetectorTyphosBeamStats`` Now sub-classes
  ``PCDSAreaDetectorTyphosTrigger``
- ``TuttiFrutti``: Change camera class to ``LasBasler``

New Devices
-----------
- ``BaslerBase``: Base class for inheriting some Basler-specific PVs.
- ``Basler``: Class for "typical" Basler deployed in a hutch.
- ``LasBasler``: Class for more laser-specific Basler cameras.
- ``MPODChannelHV``, and ``MPODChannelLV`` for MPOD high voltage and
  low voltage channels, respectively.
- Added the ``AcromagChannel`` that supports the creation of an Acromag Channel signal
- Added ``mirror.XOffsetMirrorBend`` class for offset mirrors with benders.
- Added ``mirror.XOffsetMirrorSwitch``.
  This is nearly identical to mirror.XOffsetMirror but with no Bender and
  vertical axes YLEFT/YRIGHT instead of YUP/YDWN.
- Added ``spectrometer.Mono``,
  this includes all motion axes and Pytmc signals for SP1K1-MONO system

Bugfixes
--------
- ``lasers/elliptec.py``: Fix conflict with BlueSky interface and 'stop'
  signal.
- For event scheduling, ensure that we only try to put into the queue
  if event_thread is not None. This resolves some of the startup terminal spam
  in lucid.
- PTMPLC ilk pv was incorrect, changed from ILK_STATUS_RBV to ILK_OK_RBV
- Create a default status info message for devices that have
  errors in constructing their status.

Maintenance
-----------
- Added more documentation to methods and ``LensStack`` class.
- Refactored be lens classes to use ``pcdscalc.be_lens_calcs``
- Add laser imports to :mod:`pcdsdevices.device_types`.  Test fixtures now
  verify imported laser devices' tab completion settings.

Contributors
------------
- cristinasewell
- ghalym
- hhslepicka
- jsheppard95
- klauer
- sfsyunus
- tjohnson
- zllentz


v3.2.0 (2020-10-23)
===================

Device Updates
--------------
- PCDSAreaDetectorTyphos: Added a camera viewer button to the class to open a
  python camera viewer for the camera. Removed the old 'cam_image' viewer in
  favor of this new viewer.
- El3174AiCh: Added ESLO, EOFF fields, removed EGUH, EGUL

New Devices
-----------
- SmarActTipTilt: Class for bundling two SmarActOpenLoop axis classes together
  into a single device for Typhos screen generation and interactive use.
- Added VGC_2S, a new valve class that extends the VGC
  with the addition of a second setpoint and hysteresis.

Contributors
------------
- ghalym
- tjohnson


v3.1.0 (2020-10-21)
===================

API Changes
-----------
- The `SxrGmD` device has been removed from `beam_stats` module. SXR has been
  disassembled and the GMD was moved into the EBD. Its MJ PVs was not working
  anymore.

Device Updates
--------------
- Added RTD PVs to KBOMirror class for bender actuators
- Added PTYPE PV to SmarAct class
- Added metadata to SmarAct jog pvs for better screens
- Added additional PVs to lasers/elliptec.py classes
- TuttiFruttiCls: Added an option to specify the controller channel for
  Thorlabs Elliptec sliders.
- Added the Thorlabs WFS class to the TuttiFrutti class.

New Devices
-----------
- Add XYTargetGrid, an interactive utility class for managing a target grid
  oriented normal to the beam, with regular X-Y spacing between targets.
- PCDSAreaDetectorTyphosBeamStats, a variant of PCSDAreaDetectorTyphos that
  includes centroid information and the crosshair PVs.
- KBOMirror Class: Kirkpatrick-Baez Mirror class, X, Y, Pitch, Bender axes
- FFMirror Class: Kirkpatrick-Baez Mirror without Bender axes. (Fixed focus)
- LAMP motion Class for the LAMP endstation TMO. This includes the following motion axes:

  - Gas Jet X/Y/Z Axes
  - Gas Needle X/Y/Z Axes
  - Sample Paddle X/Y/Z Axes

- A new LCLS class has been added to the `beam_stats` module that contains PVs
  related to the Lcls Linac Status, as well as a few functions to support with
  checking the BYKIK status, turning it On and Off, and setting the period.
- SmarActOpenLoopPositioner: Class intended for performing Bluesky scans using
  open-loop SmarAct motors.

Bugfixes
--------
- Corrected X/Y error in KBOMirror and FFMirror classes
- Fix issues with L2SI Reflaser Picos being unable to successfully move.
  This was because they were using the wrong motor class, which had extra
  PVs that would never connect.
- Fixed a bug preventing instantiation of the Elliptec sliders in the
  TuttiFrutti device.

Maintenance
-----------
- Add prefix and lightpath tests for KBOMirror.

Contributors
------------
- cristinasewell
- jsheppard95
- sfsyunus
- tjohnson
- zllentz


v3.0.0 (2020-10-07)
===================

API Changes
-----------
- The calculations for `alio_to_theta` and `theta_to_alio` in `ccm.py`
  have been reverted to the old calculations.
- User-facing move functions will not be able to catch the
  :class:`~ophyd.utils.LimitError` exception.  These interactive methods are
  not meant to be used in scans, as that is the role of bluesky.

Features
--------
- :class:`pcdsdevices.attenuator.AT2L0` now has a textual representation of
  filter status, and supports the move interface by way of transmission values.
- :class:`~pcdsdevices.pseudopos.SyncAxes` has been adjusted to support
  scalar-valued pseudopositioners, allowing for more complex devices to be kept
  in lock-step motion.
- :class:`~pcdsdevices.pseudopos.PseudoPositioner` position tuples, when of
  length 1, now support casting to floating point, meaning they can be used
  in many functions which only support floating point values.
- Added signal annotations for auto-generated notepad IOC support.

Device Updates
--------------
- Add event/trigger information to PPM, XPIM.
- Reclassify twincat motor and states error resets as "normal" for
  accessibility.
- Add PMPS maintenance/config PVs class for TwinCAT states devices,
  propagating this to all consumers.

New Devices
-----------
- Adds :class:`~pcdsdevices.lxe.LaserTimingCompensation` (``lxt_ttc``) which
  synchronously moves :class:`LaserTiming` (``lxt``) with
  :class:`~pcdsdevices.lxe.TimeToolDelay` (``txt``) to compensate so that the
  true laser x-ray delay by using the ``lxt``-value and the result of time tool
  data analysis, avoiding double-counting.
- Adds :class:`~pcdsdevices.lxe.TimeToolDelay`, an alias for
  :class:`~pcdsdevices.pseudopos.DelayNewport` with additional contextual
  information and room for future development.
- Add LaserInCoupling device for TMO.
- Add ArrivalTimeMonitor device for TMO.
- Add ReflaserL2SI device for TMO.

Bugfixes
--------
- Fixed a typo in a ``ValueError`` exception in
  :meth:`pcdsdevices.state.StatePositioner.check_value`.
- A read-only PV was erroneously marked as read-write in
  :class:`pcdsdevices.gauge.GaugeSerialGPI`, component ``autozero``.
  All other devices were audited, finding no other RBV-related read-only items.
- The direction of :class:`LaserTiming` (``lxt``) was inverted and is now
  fixed.
- Allow setting of :class:`~ophyd.EpicsMotor` limits when unset in the motor
  record (i.e., ``(0, 0)``) when using
  :class:`~pcdsdevices.epics_motor.EpicsMotorInterface`.

Maintenance
-----------
- Added a copy-pastable example to
  :class:`~pcdsdevices.component.UnrelatedComponent` to ease creation of new
  devices.
- Catch :class:`~ophyd.utils.LimitError` in all
  :class:`pcdsdevices.interface.MvInterface` moves, reporting a simple error by
  way of the interface module-level logger.

Contributors
------------
- cristinasewell
- klauer
- zlentz


v2.11.0 (2020-09-21)
===================

API Changes
-----------
- :class:`BaseInterface` no longer inherits from :class:`ophyd.OphydObject`.
- The order of multiple inheritance for many devices using the LCLS-enhanced
  :class:`BaseInterface`, :class:`MvInterface`, and :class:`FltMvInterface` has
  been changed.
- Added :class:`pcdsdevices.interface.TabCompletionHelperClass` to help hold
  tab completion information state and also allow for tab-completion
  customization on a per-instance level.
- :class:`~pcdsdevices.interface.Presets` ``add_hutch`` (and all similar
  ``add_*``) methods no longer require a position.  When unspecified, the
  current position is used.

Features
--------
- For :class:`pcdsdevices.pseudopos.DelayBase`, added
  :meth:`~pcdsdevices.pseudopos.DelayBase.set_current_position` and its related
  component `user_offset`, allowing for custom offsets.
- Epics motors can now have local limits updated per-session, rather than
  only having the option of the EPICS limits. Setting limits attributes will
  update the python limits, putting to the limits PVs will update the limits
  PVs.
- Add PVPositionerDone, a setpoint-only PVPositioner class that is done moving
  immediately. This is not much more useful than just using a PV, but it is
  compatibile with pseudopositioners and has a built-in filter for ignoring
  small moves.
- Moves using mv and umv will log their moves at info level for interactive
  use to keep track of the sessions.
- Add ``user_offset`` to :class:`~pcdsdevices.signal.UnitConversionDerivedSignal`,
  allowing for an arbitrary user offset in user-facing units.
- Add ``user_offset`` signal to the :class:`pcdsdevices.lxe.LaserTiming`, by
  way of :class:`~pcdsdevices.signal.UnitConversionDerivedSignal`, offset
  support.

Device Updates
--------------
- CCM energy limited to the range of 4 to 25 keV
- CCM theta2fine done moving tolerance raised to 0.01
- Beam request default move start tolerance dropped to 5eV

New Devices
-----------
- Add WaveFrontSensorTarget for the wavefront sensor targets (PF1K0, PF1L0).
- Add TwinCATTempSensor for the updated twincat FB with corrected PV pragmas.

Bugfixes
--------
- Adds hints to the :class:`pcdsdevices.lxe.LaserTiming` class for
  ``LiveTable`` support.
- umv will now properly display position and completion status after a move.
- Tab completion for many devices has been fixed. Regression tests have been
  added.
- Fix bug in PulsePickerInOut where it would grab only the first section of
  of the PV instead of the first two
- Tweak will feel less "janky" now and give useful feedback.
- Tweak now accepts + and - as valid inputs for changing the step size.
- Tweak properly clears lines between prints.
- Fix issue where putting to the limits property would update live PVs,
  contrary to the behavior of all other limits attributes in ophyd.
- Fix issue where doing a getattr on the limits properties would fetch
  live PVs, which can cause slowdowns and instabilities.
- Preset methods are now visible when not in engineering mode. (#576)
- Rework BeamEnergyPositioner to be setpoint-only to work properly
  with the behavior of the energy PVs.
- FltMvPositioner.wm will now return numeric values if the position
  value is a tuple. This value is the first element of the tuple, which
  for pseudo positioners is a value that can be passed into move and have
  it do the right thing. This resolves consistency issues and fixes bugs
  where mvr and umvr would fail.
- Fixed a race condition in the EventSequencer device's status objects. Waiting
  on these statuses will now be more reliable.
- Fix issue where converting units could incur time penalties of up to
  7 seconds. This should take around 10ms now.
- Fix bug on beam request where you could not override the tolerance
  via init kwarg, despite docstring's indication.

Maintenance
-----------
- Establish DOC conventions for accumulating release notes from every
  pull request.
- Tweak refactored for maintainability.
- Use more of the built-in ophyd mechanisms for limits rather than
  relying on local overrides.

Contributors
------------
- klauer
- zllentz
- zrylettc


v2.10.0 (2020-08-21)
====================

Features
--------
- Add LookupTablePositioner PseudoPositioner base class for moves
  based on a calibration table.
- Add UnitConversionDerivedSignal as a Signal class for converting
  EPICS units to more desirable units for the user.
- Add units to the IPython prettyprint repr.

Device Updates
--------------
- Add Vernier integration into the CCM class using BeamEnergyRequest.

New Devices
-----------
- Add support for Thorlabs WFS40 USB Wavefront Sensor Camera.
- Add LaserEnergyPositioner PseudoPositioner (lxe) using
  LookupTablePositioner.
- Add LaserTiming PVPositioner (lxt) using UnitConversionDerivedSignal.
- Add BeamEnergyRequest PVPositioner for requesting beam energies in eV from
  ACR.


v2.9.0 (2020-08-18)
===================

Features
--------
- Devices will now show detailed status information when returned
  in the ipython terminal.

Device Updates
--------------
- Update docs on FSV fast shutter valve
- Update AT2L0 with state positioners and calculator
- Update Elliptec classes for cleaner implementation
- Add missing CCM motors and fix the energy motion (no vernier yet)
- Add HDF5 plugin to PCDSAreaDetectorEmbedded

New Devices
-----------
- Add support for SmarAct motors
- Add attenuator calculator device for Ken's new calculator
- Add support for TuttiFruitti diagnostic stack

Bugfixes
--------
- Fix typo in PV name of BeckhoffJet slits


v2.8.0 (2020-07-24)
===================

Features
--------
- Expand variety schema support and add dotted dictionary access.

Device Updates
--------------
- Update various vacuum char waveforms with ``string=True`` for proper
  handling in ``typhos``.
- Add various missing vacuum PVs to various vacuum devices.
- Switch twincat state device error reset to ``kind=config`` so it shows up
  by default in ``typhos``.
- Update LCLS-II imagers to use the new ``AreaDetectorTyphos``.
- The following devices now have ``lightpath`` support:
  - ``FeeAtt``
  - ``FEESolidAttenuator``
  - ``XOffsetMirror``
  - ``PPM``
  - ``XPIM``
  - ``PowerSlits``
  - ``Kmono``
  - ``VRC`` and all subclasses, such as ``VGC``
  - ``VFS``
- Update ``XOffsetMirror`` ``y_up``, ``x_up``, and ``pitch`` to
  ``kind=hinted`` (previously ``normal``). These axes are usually the
  most important.
- Rename ``PPM.y_states`` and ``XPIM.y_states`` to ``target`` for reduced
  redundancy in screens. The only name is aliased via a property.
- ``PowerSlits`` now have a feature set on par with the old slits.
- Update ``VFS`` ``valve_position`` and ``vfs_state`` to ``kind=hinted``
  (previously ``normal``) for more focused statuses.

New Devices
-----------
- Add support for Qmini Spectrometer.
- Add ``AreaDetectorTyphos`` class for optimized screen view of most used
  area detector signals.
- Add ``RTDSL0`` and ``RTDSK0`` to support the rapid turnaround diagnostic
  station configurations.

Bugfixes
--------
- Fix issue with failing callback in ``IMS`` from upstream ``ophyd`` change.

Maintenance
-----------
- Switch from using ``cf-units`` to ``pint`` for portability.
- Add the following helpers:
  - ``interface.LightpathMixin`` to help establish ``lightpath`` support.
  - ``signal.NotImplementedSignal`` to help devices that will expand later.
  - ``signal.InternalSignal`` to help implement read-only signals that can
    be updated by the parent class.
  - ``utils.schedule_task`` to help interface with the ``ophyd`` callback
    queues.
- The ``slits`` module has been refactored to accomodate both old and new
  slits.


v2.7.0 (2020-07-01)
===================

Features
--------
- Add component variety metadata and schema validation.

Device Updates
--------------
- Add many components to ``PIPPLC`` class, adjust component
  ``kinds`` to be more appropriate, and fix errant PV names.
- Update component names on ``VVC`` for clarity, and pvnames for accuracy.
- Update ``XPIM`` class to reflect additional IOC features.
- Update docs and metadata on all LCLS 2 imager classes.
- Update spammy TwinCAT state config parameters to omitted.
- Add interlock device information to ``VGC``.
- Add ``SPMG`` field to ``BeckhoffAxis``.

New Devices
-----------
- Add ``SxrTestAbsorber`` class.
- Add ``ZoomTelescope`` to support MODS zoom telescope.
- Add ``El3174AiCh`` to support EK9000 module.
- Add ``EnvironmentalMonitor`` to support MODS environmental monitors.
- Add support for ThorLabs Elliptec motors for MODS.
- Add ``Ebara_EV_A03_1`` class for specific roughing pump support.
- Migrate SDS jet tracking classes into this repo.
- Add ``VFS`` class to support fast shutters.

Maintenance
-----------
- Remove monkeypatch of ``EventSequence`` in tests, as it was no longer needed.
- Update dependency from ``cf_units`` to its renamed ``cf-units``.
- xfail test that fails with ``bluesky=1.6.2``


v2.6.0 (2020-05-21)
===================

Features
--------
- ``happi`` entry points have been moved to this library for proper
  modularization.
- Area detectors embedded inside of larger devices have been made
  considerably smaller to improve performance in other applications,
  for example in ``typhos``.

Bugfixes
--------
- Provide ``FakePytmcSignal`` for testing in external libraries. This
  fixes issues with fake devices not working if they contain ``PytmcSignal``
  instances outside of the ``pcdsdevices`` testing suite.
- Fix various issues related to moving to ``ophyd`` ``v1.5.0``.
- This library is now importable on win32.

Docs
----
- Docstrings now conform to the new pcds standards.


v2.5.0 (2020-04-15)
===================

Features
--------
- Add classes for Goniometers, Von Hamos spectrometers, Beckhoff liquid jets, TimeTools, and PFLSs
- Add ``UnrelatedComponent`` as a helper for writing devices with many prefixes

Bugfixes
--------
- Fix TwinCAT states enum states
- Add missing packages to requirements file
- Compatibility with newest ``ophyd``

Misc
----
- Add pre-commit hooks to help with development flow
- Add license file to manifest
- Eliminate ``m2r`` docs dependency


v2.4.0 (2020-03-12)
===================

Features
--------
- Add ``PytmcSignal``
- Add ``PPM``, ``XPIM``, ``XOffsetMirror``, and ``Kmono`` classes
- Update ``IPM`` and ``PIM`` modules to better match physical devices
- Add various helper classes for TwinCAT devices
- Stubs created for attenuators, ``RTD``, and ``PowerSlit``
- Make ``cmd_err_reset`` in ``BeckhoffAxisPLC`` accessible in Typhos

API Changes
-----------
- Changed ``set_point_relay`` to ``pump_on_status``, ``at_vac_sp`` to
  ``at_vac_setpoint`` and added ``pump_state`` to ``PIPPLC``

- Changed ``at_vac_sp`` to ``at_vac_setpoint``, ``at_vac_hysterisis``
  to ``setpoint_hysterisis``, and added mps_state to ``VGC``

Bugfixes
--------
- Make ``protection_setpoint`` writeable in ``GCCPLC``
- Make ``state`` writeable in ``VCN``

Misc
----
- Allow build docs failure to speed up overall CI
- Specify old working conda version as temporary solution for
  build failures


v2.3.0 (2020-02-05)
===================

Features
--------
- Make everything compatible with the upcoming ``ophyd`` ``v1.4.0``
- Add be lens calculations port from old python system


v2.2.0 (2020-01-22)
===================

Features
--------
- Add a bunch vacuum-related classes for L2SI

Misc
----
- Fix an issue with the doctr deploy key


v2.1.0 (2020-01-10)
===================

Features
--------
- Add ``screen`` method to ``PCDSMotorBase`` to open the motor expert screen
- Add tab completion filtering via whitelists as the first feature of the
  ``engineering_mode`` switch. This was implemented because the tab
  completion on ophyd devices is extremely overwhelming.
  Use ``set_engineering_mode(bool)`` to turn ``engineering_mode`` on or off.
  The default is "on", which means "everything is normal".
  Turning ``engineering_mode`` off enables the whitelist filtering,
  and in the future may also have other effects on the user interface.
- Add ``dc_devices`` module for components from the new DC power system.
  This currently contains the ``ICT`` and related classes.

Misc
----
- Fixed a race condition in the tests
- Clean up the Travis CI configuration
- Pin pyepics to >=3.4.1 due to a breaking change from python 3.7.6


v2.0.0 (2019-06-28)
===================

Features
--------
- Add ``gauge`` and ``pump`` modules
- Add ``Acromag`` and ``Mesh`` classes
- Add ``motor`` subdevice to state record devices
- Add ``status`` string to ``BeckhoffAxis``

API Breaks
----------
- State devices no longer have the ``readback`` signal, as it is redundant
  with the new ``motor`` subdevice
- ``PCDSDetector`` has been renamed to ``PCDSAreaDetector`` for clarity.
  ``PCDSDetectorBase`` is also renamed to ``PCDSAreaDetectorBase``.

Bugfixes
--------
- Fix PVs in ``BeckhoffAxis``

Misc
----
- Officially build for ``python=3.7``


v1.2.0 (2019-03-08)
===================

Features
--------
- Add all common plugins to ``PCDSDetector``
- ``EventSequencer`` now accepts human-readable sequences

Fixes
-----
- Fix debug PV names in ``BeckhoffAxis``

Misc
----
- Add a py37 build to the CI
- Remove outdated hotfix for ``FakeEpicsSignal`` in tests
- Fix misc testing errors


v1.1.0 (2018-10-26)
===================

Features
--------
- Support for reading and writing sequences to and from the ``EventSequencer``
- Add ``Motor`` factory function for choosing which motor class to use based
  on the text in the ``prefix``.

Bugfixes
--------
- ``IMS`` class will no longer get its ``.SPG`` field stuck on ``paused`` or
  ``stopped`` when a scan is interrupted. Scans will start even if these
  fields are blocked.
- Update out-of-date ``requirements.txt`` file for ``pip``
- Pin ``matplotlib`` to ``<3`` to avoid import incompatibility pitfalls, and
  confine the ``matplotlib`` imports to function scope instead of module scope
  to avoid having a backend be set on import.


v1.0.0 (2018-10-12)
===================

Features
--------
- Display current position in ``umv`` progress bars
- Added ``ophyd`` ``Kind`` specification to every device in the library
- Added ``.DESC`` field to standard motor interface
- Added ``trigger`` to event sequencer and other changes to make it more
  useful in scans
- Added ``CCM`` class
- Added ``BeckhoffAxis`` class for the Beckhoff ADS-based motor record
- Added evr ``Trigger`` class for configuring evr triggers
- Added ``FeeAtt`` class for the wonky FEE attenuator
- Clean up ``Reflaser`` classes
- Added ``LensStack`` class python2 port for the xyz focusing assembly
  (not fully complete)
- Added ``DelayStage`` class for laser delay stages
- Added ``SyncAxes`` class for synchronizing axes e.g. tables, ccm
- Added ``keypress`` utilities python2 port
- Added ``wm_update`` python2 port to ``FltMvInterface``. This is essentially a
  ``camonitor``.
- Added ``mv_ginput`` python2 port to ``FltMvInterface``
- Added per-class icons to be picked up by ``lightpath`` and other
  applications

Bugfixes
--------
- Use ``IMAGE2`` instead of ``IMAGE1`` as the area detector default, because
  this is the low rate or binned image. Avoid sending huge images quickly
  through python processes.
- Prevent issue where presets from same-named device would interfere with
  eachother.
- Attenuator subclasses now have sane names
  (previously ``Attenuator1234567``, for example).
- Split the XPP and XCS lodcm foils (they are different).
- Warn the user about using certain classes directly when they need to be
  subclassed.
- Raise errors for any invalid state in a state positioner, not just the
  ``Unknown`` state.
- Add ``SUB_STATE`` subscription types for ``OffsetMirror`` and ``Attenuator``
- Valve interlock had inverted logic

Maintenance
-----------
- Standardize component imports as ``import Component as Cpt``
- Move some interlocks into ``check_value`` instead of ad-hoc locations
- Misc travis fixes and improvements
- State devices are more forgiving with certain inputs
- Clean up the `Slit` interface for ``lightpath``

API Breaks
----------
- Rework and improve various simulated hardware, removing old ``sim`` modules.
- Require some newer modules


v0.8.0 (2018-05-27)
===================

Features
--------
- Added `AvgSignal` class. This can be used when you want to
  run a callback on a rolling average of a ``Signal``. (#227)
- Added an average of the gas detector energy to the `BeamStats` class (#227)
- Implemented motor `.SPG` field from LCLS motor record into PCDSMotorBase (#236)

Bugfixes
--------
- Fix a bug where the `LODCM` class had a ``readback`` signal with an invalid PV. (#232)
- Fix a bug where the tests would never pass, ever (#238)

v0.7.0 (2018-05-08)
===================

Features
--------
- Revamp states handling for devices like IPM and XFLS (#205)
- Add a `BeamStats` class (#200)
- Add an `EventSequencer` class (#196)
- Add ``DISP`` field to `PCDSMotorBase` (#192)

Bugfixes
--------
- Fix a bug where preset saving could break the session if passed bad arguments (#218)
- Fix a bug where malformed states could fail silently or cryptically (#216)
- Fix a bug where the mirror states were reversed (#215)
- Fix a bug where IMS velocity limits were ignored (#209)

v0.6.0 (2018-04-05)
===================

Features
--------
- Improved documentation (#170)
- Recreated the presets feature from existing hutch_python deployments.
  This allows operators to record the positions of anything that implements
  the `FltMvInterface` to a ``YAML`` file. This helps keep track of various
  important experimental motor positions that are too dynamic to place in EPICS. (#187)

Bugfixes
--------
- Fixed a rare race condition in the testing suite (#189)

CI
--
- Testing suite now uses the conda-forge build of ophyd instead of NSLS-II lightsource2-tag (#191)

v0.5.0 (2018-03-08)
===================

API Changes
-----------
- The `pcdsdevices.EpicsMotor` has been replaced by
  `pcdsdevices.PCDSMotorBase` and three child classes
  `IMS`, `Newport` and `PMC100`. This is an attempt to have a reasonable MRO
  for the discrepenacies between all our different implementations
  of the EPICS Motor Record (#167)
- Due to the growing complication of the Daq class and related utilities,
  all related functions were moved to `<https://pcdshub.github.io/pcdsdaq>`_ (#168)
