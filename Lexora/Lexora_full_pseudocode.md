ver. 1.0

```pseudo

// ===== Enums =====

// Exclusively marks whether a Lexon is temporal or spatial (or unspecified)
Enum SpatiotemporalKind:
    TEMPORAL
    SPATIAL

// Temporal aspect state (coarse)
Enum DurationState:
    COMPLETED    // bounded/achieved event or state
    ONGOING      // unbounded/continuing process

// Grammatical number
Enum GrammaticalNumber:
    SINGULAR     // exactly one
    PLURAL       // more than one

// Intensity polarity (qualitative direction)
Enum IntensityPolarity:
    STRONG       // stronger / higher degree
    WEAK         // weaker / lower degree

// Information structure (discourse status)
Enum InformationStructure:
    TOPIC        // what the clause/phrase is about (given/anchor)
    FOCUS        // highlighted/new/contrastive content

// Evidentiality (information source)
Enum EvidentialityType:
    INFERENCE
    ASSUMPTION
    HEARSAY
    QUOTATIVE

// Modality (necessity/possibility/deontic/ability)
Enum Modality:
    CAN         // ability/possibility
    MUST        // strong obligation/necessity
    SHOULD      // weak obligation/recommendation
    POSSIBLE    // epistemic possibility
    NECESSARY   // epistemic necessity

// Degree kind for comparison/equality
Enum DegreeKind:
    COMPARATIVE   // comparative grade ("-er", "more ...")
    SUPERLATIVE   // superlative grade ("-est", "most ...")
    EQUAL         // equality/as ... as ...

// Minimal universal participant roles (with explanations)
Enum RoleType:
    ACTOR        // doer/initiator; merges Agent and Causer
    UNDERGOER    // affected participant; merges Patient and Theme
    EXPERIENCER  // sentient experiencer in psych predicates
    STIMULUS     // trigger/stimulus of experience/perception
    RECIPIENT    // endpoint of transfer; Beneficiary can be subtype
    CONTENT      // propositional/content complement (said/thought/caused)
    COMITATIVE   // co-participant "with" (companion/associate)
    POSSESSOR    // possessor/owner relation
    STANDARD     // comparison standard ("than X", "to X")
    DOMAIN       // comparison domain/set ("in/among Y")

// Mood / clause type
Enum Mood:
    INDICATIVE
    IMPERATIVE
    SUBJUNCTIVE
    INTERROGATIVE
    EXCLAMATIVE

// Coordination type (logical/semantic)
Enum CoordinationType:
    AND         // conjunctive ("and", simple listing)
    OR          // disjunctive ("or", alternatives)
    BUT         // adversative ("but", contrast)
    APPOSITIVE  // appositive pairing ("John, teacher")

// Register / honorifics
Enum Register:
    POLITE      // polite/formal addressee honorific
    HONORIFIC   // subject honorification
    HUMBLE      // speaker humbling

// Quantification type (logical force)
Enum QuantType:
    EXISTENTIAL   // some/any/a... (∃)
    UNIVERSAL     // every/all/each (∀)
    PROPORTION    // most/many/few/percentages

// Definiteness (article-like semantics)
Enum Definiteness:
    DEFINITE
    INDEFINITE
    GENERIC

// Specificity (speaker’s intended identifiability)
Enum Specificity:
    SPECIFIC
    NONSPECIFIC


// ===== Value/Relation Objects =====

// Generic directional span usable for time or space.
// Semantics (temporal vs spatial) are determined by the host Lexon.
Class Direction
    Attributes:
        from: Lexon* | null   // origin (temporal or spatial)
        to:   Lexon* | null   // goal   (temporal or spatial)

    Constructor(fromObj: Lexon* | null, toObj: Lexon* | null):
        from = fromObj
        to   = toObj

    Method setFrom(x: Lexon* | null):  from = x
    Method setTo(x: Lexon* | null):    to   = x

// Number specification (grammatical + cardinal)
Class NumberSpec
    Attributes:
        grammatical: GrammaticalNumber | null   // grammatical number
        value: double | null                    // concrete cardinal value (e.g., 1, 2, 3)

    Constructor(g: GrammaticalNumber | null, v: double | null):
        grammatical = g
        value = v

// Numeric magnitude with unit label (e.g., 1 N, 3 m/s, 2/week)
Class ValueWithUnit
    Attributes:
        value: double    // numeric magnitude
        unit: string     // unit label (e.g., "N", "m/s", "/day", "%")

    Constructor(v: double, u: string):
        value = v
        unit  = u

// Intensity specification (qualitative and/or quantitative)
Class IntensitySpec
    Attributes:
        polarity: IntensityPolarity | null   // STRONG / WEAK (optional)
        value: ValueWithUnit | null          // measured intensity (optional)

    Constructor(p: IntensityPolarity | null, val: ValueWithUnit | null):
        polarity = p
        value    = val

// Generic role assignment container
Class RoleAssignment
    Attributes:
        role: RoleType
        filler: Lexon*            // participant that fills the role

    Constructor(r: RoleType, x: Lexon*):
        role = r
        filler = x

// Coordination container (only type + members)
Class Coordination
    Attributes:
        type: CoordinationType    // logical/semantic type of coordination
        members: Lexon*[]         // coordinated elements (2 or more)

    Constructor(t: CoordinationType, xs: Lexon*[]):
        type = t
        members = xs

    Method setType(t: CoordinationType):      type = t
    Method setMembers(xs: Lexon*[]):          members = xs
    Method addMember(x: Lexon*):              members.append(x)


// ===== Core Object =====

Class Lexon
    Attributes:
        // lexical surface form; null means an abstract structural node
        text: string | null

        // structural relations
        dominatee: Lexon*                     // head-dependent dominance (e.g., S→V, copular N→Adj/N)
        modifee: Lexon*                       // modifier→modifee (Adj→N, Adv→V, phrase→clause, etc.)
        coordination: Coordination* | null    // coordination metadata (type + members)

        // spatiotemporal annotations
        spatioTemporalKind: SpatiotemporalKind | null   // TEMPORAL / SPATIAL / null
        direction: Direction* | null        // generic span (used for time or space)

        // temporal annotations
        time: Lexon* | null                     // temporal point ("when")
        durationState: DurationState | null     // COMPLETED / ONGOING

        // spatial annotations
        place: Lexon* | null                  // where (location)

        // quantification
        number: NumberSpec* | null            // grammatical + cardinal number
        quantType: QuantType | null           // existential/universal/proportion

        // definiteness/specificity
        definiteness: Definiteness | null
        specificity: Specificity | null

        // intensity
        intensity: IntensitySpec* | null      // qualitative/quantitative intensity

        // frequency (rate or lexical adverbial)
        frequency: Lexon* | ValueWithUnit | null

        // semantic roles / adjuncts
        instrument: Lexon* | null             // instrument/means ("with X", "by X")
        roles: RoleAssignment[]               // participant roles bound to this predicate/node

        // polarity & voice
        negativePolarity: boolean = false     // false = affirmative
        passiveVoice: boolean = false         // false = active
        causativeVoice: boolean = false       // false = non-causative
        middleVoice: boolean | null           // middle-like alternation (null = unspecified)
        reflexiveVoice: boolean | null             // coreference of actor and undergoer
        reciprocalVoice: boolean | null            // mutual relation among participants

        // discourse-level annotations
        infoStructure: InformationStructure | null   // topic/focus
        evidentiality: EvidentialityType | null      // information source

        // register/honorifics
        register: Register | null       // null = plain

        // modality/mood
        modality: Modality | null           // can/must/should/possible/necessary
        mood: Mood | null                   // clause type

        // comparison degree
        degreeKind: DegreeKind | null       // comparative/superlative/equal

    Constructor(inputText: string | null):
        text = inputText

    // structural setters
    Method SetDominatee(x: Lexon*):                 dominatee = x
    Method setModifee(x: Lexon*):                   modifee = x
    Method setCoordination(c: Coordination*):       coordination = c

    // temporal & spatial setters
    Method setSpatiotemporalKind(k: SpatiotemporalKind):    spatioTemporalKind = k
    Method setDirection(dir: Direction*):                   direction = dir
    Method setTime(x: Lexon*):                              time = x
    Method setDurationState(state: DurationState):          durationState = state
    Method setPlace(x: Lexon* | null):                      place = x

    // quantification & noun semantics setters
    Method setNumber(spec: NumberSpec*):            number = spec
    Method setQuantType(q: QuantType):              quantType = q
    Method setDefiniteness(d: Definiteness):        definiteness = d
    Method setSpecificity(s: Specificity):          specificity = s

    // intensity & frequency setters
    Method setIntensity(spec: IntensitySpec*):      intensity = spec
    Method setFrequency(x: Lexon* | ValueWithUnit | null): frequency = x

    // role/semantic setters
    Method setInstrument(x: Lexon* | null):         instrument = x
    Method addRole(r: RoleType, x: Lexon*):         roles.append(new RoleAssignment(r, x))

    // polarity & voice setters
    Method setNegativePolarity(flag: boolean):      negativePolarity = flag
    Method setPassiveVoice(flag: boolean):          passiveVoice = flag
    Method setCausativeVoice(flag: boolean):        causativeVoice = flag
    Method setMiddleVoice(flag: boolean | null):    middleVoice = flag
    Method setReflexiveVoice(flag: boolean | null):      reflexive = flag
    Method setReciprocalVoice(flag: boolean | null):     reciprocal = flag

    // discourse & register setters
    Method setInformationStructure(v: InformationStructure): infoStructure = v
    Method setEvidentiality(v: EvidentialityType):           evidentiality = v
    Method setRegister(r: Register):                         register = r

    // modality/TAM setters
    Method setModality(m: Modality):                         modality = m
    Method setMood(md: Mood):                                mood = md

    // comparison setter
    Method setDegreeKind(k: DegreeKind):                     degreeKind = k
```

