
======
ggwave
======

Tiny data-over-sound library.


.. code:: python

    # generate audio waveform for string "hello python"
    waveform = ggwave.encode("hello python")

    # decode audio waveform
    text = ggwave.decode(instance, waveform)


--------
Features
--------

* Audible and ultrasound transmissions available
* Bandwidth of 8-16 bytes/s (depending on the transmission protocol)
* Robust FSK modulation
* Reed-Solomon based error correction

------------
Installation
------------
::

    pip install ggwave

---
API
---

encode()
--------

.. code:: python

    encode(payload, [protocolId], [volume], [instance])

Encodes ``payload`` into an audio waveform.


Output of ``help(ggwave.encode)``:

.. code::

    built-in function encode in module ggwave
    
    encode(...)
        Encode payload into an audio waveform.
        @param {string} payload, the data to be encoded
        @return Generated audio waveform bytes representing 16-bit signed integer samples.
    

decode()
--------

.. code:: python

    decode(instance, waveform)

Analyzes and decodes ``waveform`` into to try and obtain the original payload.
A preallocated ggwave ``instance`` is required.


Output of ``help(ggwave.decode)``:

.. code::

    built-in function decode in module ggwave
    
    decode(...)
        Analyze and decode audio waveform to obtain original payload
        @param {bytes} waveform, the audio waveform to decode
        @return The decoded payload if successful.
    


-----
Usage
-----

* Encode and transmit data with sound:

.. code:: python

    import ggwave
    import pyaudio

    p = pyaudio.PyAudio()

    # generate audio waveform for string "hello python"
    waveform = ggwave.encode("hello python", protocolId = 1, volume = 20)

    print("Transmitting text 'hello python' ...")
    stream = p.open(format=pyaudio.paFloat32, channels=1, rate=48000, output=True, frames_per_buffer=4096)
    stream.write(waveform, len(waveform)//4)
    stream.stop_stream()
    stream.close()

    p.terminate()

* Capture and decode audio data:

.. code:: python

    import ggwave
    import pyaudio

    p = pyaudio.PyAudio()

    stream = p.open(format=pyaudio.paFloat32, channels=1, rate=48000, input=True, frames_per_buffer=1024)

    print('Listening ... Press Ctrl+C to stop')
    instance = ggwave.init()

    try:
        while True:
            data = stream.read(1024, exception_on_overflow=False)
            res = ggwave.decode(instance, data)
            if (not res is None):
                try:
                    print('Received text: ' + res.decode("utf-8"))
                except:
                    pass
    except KeyboardInterrupt:
        pass

    ggwave.free(instance)

    stream.stop_stream()
    stream.close()

    p.terminate()

----
More
----

Check out `<http://github.com/ggerganov/ggwave>`_ for more information about ggwave!

-----------
Development
-----------

Check out `ggwave python package on Github <https://github.com/ggerganov/ggwave/tree/master/bindings/python>`_.
